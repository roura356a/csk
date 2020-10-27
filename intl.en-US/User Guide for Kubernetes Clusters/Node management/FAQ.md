# FAQ

This topic describes the common questions about node management.

-   [How do I manually upgrade the kernel of the GPU nodes in a cluster?](#section_i7t_2z5_h3n)
-   [How do I fix the issue that no container is started on a GPU node?](#section_wj1_6cu_gtc)

## How do I manually upgrade the kernel of the GPU nodes in a cluster?

You can manually upgrade the kernel of the GPU nodes in a cluster. Perform the following steps:

**Note:** The current kernel version is earlier than `3.10.0-957.21.3`.

Confirm the kernel version to which you want to upgrade. Proceed with caution.

You can perform the following steps to upgrade the kernel and NVIDIA driver.

1.  [Use kubectl to connect to a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to a cluster.md).

2.  Set the GPU node that you wan to manage to the unschedulable state. In this example, the node cn-beijing.i-2ze19qyi8votgjz12345 is used.

    ```
    kubectl cordon cn-beijing.i-2ze19qyi8votgjz12345
    
    node/cn-beijing.i-2ze19qyi8votgjz12345 already cordoned
    ```

3.  Migrate the pods on the node to other nodes.

    ```
    kubectl drain cn-beijing.i-2ze19qyi8votgjz12345 --grace-period=120 --ignore-daemonsets=true
    
    node/cn-beijing.i-2ze19qyi8votgjz12345 cordoned
    WARNING: Ignoring DaemonSet-managed pods: flexvolume-9scb4, kube-flannel-ds-r2qmh, kube-proxy-worker-l62sf, logtail-ds-f9vbg
    pod/nginx-ingress-controller-78d847fb96-5fkkw evicted
    ```

4.  Uninstall the existing nvidia-driver.

    **Note:** In this example, the driver version 384.111 is uninstalled. If your driver version is not version 384.111, download a driver from the official NVIDIA website and replace version `384.111` with your actual version number.

    1.  Log on to the GPU node. In the command-line interface \(CLI\), enter `nvidia-smi` to check the driver version.

        ```
        nvidia-smi -a | grep 'Driver Version'
        Driver Version                      : 384.111
        ```

    2.  Download the driver installation package.

        ```
        cd /tmp/
        curl -O https://cn.download.nvidia.cn/tesla/384.111/NVIDIA-Linux-x86_64-384.111.run
        ```

        **Note:** The installation package is required to uninstall the driver.

    3.  Uninstall the existing driver.

        ```
        chmod u+x NVIDIA-Linux-x86_64-384.111.run
        . /NVIDIA-Linux-x86_64-384.111.run --uninstall -a -s -q
        ```

5.  Upgrade the kernel.

    Upgrade the kernel based on your requirements.

6.  Restart the GPU node.

    ```
    reboot
    ```

7.  Log on to the GPU node to install the required kernel-devel package.

    ```
    yum install -y kernel-devel-$(uname -r)
    ```

8.  Go to the official NVIDIA website, download the required driver, and then install the driver on the node. In this example, version 410.79 is used.

    ```
    cd /tmp/
    curl -O https://cn.download.nvidia.cn/tesla/410.79/NVIDIA-Linux-x86_64-410.79.run
    chmod u+x NVIDIA-Linux-x86_64-410.79.run
    sh . /NVIDIA-Linux-x86_64-410.79.run -a -s -q
    
    warm up GPU
    nvidia-smi -pm 1 || true
    nvidia-smi -acp 0 || true
    nvidia-smi --auto-boost-default=0 || true
    nvidia-smi --auto-boost-permission=0 || true
    nvidia-modprobe -u -c=0 -m || true
    ```

9.  Check the /etc/rc.d/rc.local file to verify that the following configurations are included. If not, add the following configurations to the file.

    ```
    nvidia-smi -pm 1 || true
    nvidia-smi -acp 0 || true
    nvidia-smi --auto-boost-default=0 || true
    nvidia-smi --auto-boost-permission=0 || true
    nvidia-modprobe -u -c=0 -m || true
    ```

10. Restart kubelet and Docker.

    ```
    service kubelet stop
    service docker restart
    service kubelet start
    ```

11. Set the GPU node to the schedulable state.

    ```
    kubectl uncordon cn-beijing.i-2ze19qyi8votgjz12345
    
    node/cn-beijing.i-2ze19qyi8votgjz12345 already uncordoned
    ```

12. In the CLI, run the following command in the nvidia-device-plugin container to check the version of the driver that is installed on the GPU node.

    ```
    kubectl exec -n kube-system -t nvidia-device-plugin-cn-beijing.i-2ze19qyi8votgjz12345 nvidia-smi
    Thu Jan 17 00:33:27 2019
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 410.79       Driver Version: 410.79       CUDA Version: N/A      |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |===============================+======================+======================|
    |   0  Tesla P100-PCIE...  On   | 00000000:00:09.0 Off |                    0 |
    | N/A   27C    P0    28W / 250W |      0MiB / 16280MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                       GPU Memory |
    |  GPU       PID   Type   Process name                             Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    ```

    **Note:** If you run the `docker ps` command and find that no container is started on the GPU node, see [Failed to start a container on the GPU node]().


## How do I fix the issue that no container is started on a GPU node?

When you restart kubelet and Docker on GPU nodes where specific Kubernetes versions are installed, you may find that no container is started on the nodes after the restart.

```
service kubelet stop
Redirecting to /bin/systemctl stop kubelet.service
service docker stop
Redirecting to /bin/systemctl stop docker.service
service docker start
Redirecting to /bin/systemctl start docker.service
service kubelet start
Redirecting to /bin/systemctl start kubelet.service

docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

Run the following command to view the cgroup driver.

```
docker info | grep -i cgroup
Cgroup Driver: cgroupfs
```

The output shows that the cgroup driver is set to cgroupfs.

To fix the issue, perform the following steps:

1.  Create a copy of /etc/docker/daemon.json. In the CLI, run the following command to update /etc/docker/daemon.json.

    ```
    cat >/etc/docker/daemon.json <<-EOF
    {
        "default-runtime": "nvidia",
        "runtimes": {
            "nvidia": {
                "path": "/usr/bin/nvidia-container-runtime",
                "runtimeArgs": []
            }
        },
        "exec-opts": ["native.cgroupdriver=systemd"],
        "log-driver": "json-file",
        "log-opts": {
            "max-size": "100m",
            "max-file": "10"
        },
        "oom-score-adjust": -1000,
        "storage-driver": "overlay2",
        "storage-opts":["overlay2.override_kernel_check=true"],
        "live-restore": true
    }
    EOF
    ```

2.  In the CLI, run the following command to restart Docker and kubelet.

    ```
    service kubelet stop
    Redirecting to /bin/systemctl stop kubelet.service
    service docker restart
    Redirecting to /bin/systemctl restart docker.service
    service kubelet start
    Redirecting to /bin/systemctl start kubelet.service
    ```

3.  In the CLI, run the following command to check whether the cgroup driver is set to systemd.

    ```
    docker info | grep -i cgroup
    Cgroup Driver: systemd
    ```


