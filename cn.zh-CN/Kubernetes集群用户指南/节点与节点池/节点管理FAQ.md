# 节点管理FAQ

本文主要为您介绍节点管理常见问题。

-   [如何在已有集群的GPU节点上手动升级Kernel？](#section_i7t_2z5_h3n)
-   [修复GPU节点容器启动问题](#section_wj1_6cu_gtc)
-   [添加Kubernetes集群节点的常见问题](~~170722~~)
-   [移除节点时提示”drain-node job execute timeout”错误](~~190626~~)
-   [如何更改ACK集群中Worker节点的主机名称？](#section_cob_uvi_y06)

## 如何在已有集群的GPU节点上手动升级Kernel？

下面为您介绍如何在已有集群的GPU节点上手动升级Kernel。

**说明：** 当前kernel版本低于`3.10.0-957.21.3`。

请确认需要升级的目标kernel版本，并谨慎操作。

本文提供方案并不涉及kernel升级，仅针对在kernel升级的前提下对应的Nvidia驱动升级。

1.  [t16645.md\#](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl工具连接集群.md)。

2.  将GPU节点设置为不可调度（本例以节点 cn-beijing.i-2ze19qyi8votgjz12345为例）。

    ```
    kubectl cordon cn-beijing.i-2ze19qyi8votgjz12345
    
    node/cn-beijing.i-2ze19qyi8votgjz12345 already cordoned
    ```

3.  将要升级驱动的GPU节点进行排水。

    ```
    kubectl drain cn-beijing.i-2ze19qyi8votgjz12345 --grace-period=120 --ignore-daemonsets=true
    
    node/cn-beijing.i-2ze19qyi8votgjz12345 cordoned
    WARNING: Ignoring DaemonSet-managed pods: flexvolume-9scb4, kube-flannel-ds-r2qmh, kube-proxy-worker-l62sf, logtail-ds-f9vbg
    pod/nginx-ingress-controller-78d847fb96-5fkkw evicted
    ```

4.  卸载当前的nvidia-driver。

    **说明：** 本步骤中卸载的是版本为384.111的驱动包，如果您的驱动版本不是384.111，则需要在Nvidia官网下载对应的驱动安装包，并将本步骤中的`384.111`替换成您实际的版本。

    1.  登录到该GPU节点，通过`nvidia-smi`查看驱动版本。

        ```
        nvidia-smi -a | grep 'Driver Version'
        Driver Version                      : 384.111
        ```

    2.  下载Nvidia驱动安装包。

        ```
        cd /tmp/
        curl -O https://cn.download.nvidia.cn/tesla/384.111/NVIDIA-Linux-x86_64-384.111.run
        ```

        **说明：** 需要在安装包中卸载Nvidia。

    3.  卸载当前Nvidia驱动。

        ```
        chmod u+x NVIDIA-Linux-x86_64-384.111.run
        ./NVIDIA-Linux-x86_64-384.111.run --uninstall -a -s -q
        ```

5.  升级Kernel。

    您可以根据需要升级Kernel。

6.  重启GPU机器。

    ```
    reboot
    ```

7.  重新登录GPU节点，安装对应的kernel devel。

    ```
    yum install -y kernel-devel-$(uname -r)
    ```

8.  请到Nvidia官网下载和安装您需要的nvidia驱动， 本文以410.79为例。

    ```
    cd /tmp/
    curl -O https://cn.download.nvidia.cn/tesla/410.79/NVIDIA-Linux-x86_64-410.79.run
    chmod u+x NVIDIA-Linux-x86_64-410.79.run
    sh ./NVIDIA-Linux-x86_64-410.79.run -a -s -q
    
    warm up GPU
    nvidia-smi -pm 1 || true
    nvidia-smi -acp 0 || true
    nvidia-smi --auto-boost-default=0 || true
    nvidia-smi --auto-boost-permission=0 || true
    nvidia-modprobe -u -c=0 -m || true
    ```

9.  查看/etc/rc.d/rc.local，确认其中是否包含以下配置，如果没有请手动添加。

    ```
    nvidia-smi -pm 1 || true
    nvidia-smi -acp 0 || true
    nvidia-smi --auto-boost-default=0 || true
    nvidia-smi --auto-boost-permission=0 || true
    nvidia-modprobe -u -c=0 -m || true
    ```

10. 重启kubelet和docker。

    ```
    service kubelet stop
    service docker restart
    service kubelet start
    ```

11. 将这个GPU节点重新设置为可调度。

    ```
    kubectl uncordon cn-beijing.i-2ze19qyi8votgjz12345
    
    node/cn-beijing.i-2ze19qyi8votgjz12345 already uncordoned
    ```

12. 在GPU节点上的device plugin pod验证版本。

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

    **说明：** 如果通过`docker ps`命令，发现GPU节点没有容器被启动，请参见[修复GPU节点容器启动问题]()。


## 修复GPU节点容器启动问题

在某些特定Kubernetes版本中的GPU节点上，重启Kubelet和Docker时，发现没有容器被启动。

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

执行以下命令，查看Docker的Cgroup Driver。

```
docker info | grep -i cgroup
Cgroup Driver: cgroupfs
```

此时发现的Cgroup Driver类型是cgroupfs。

您可以按照以下操作，修复该问题。

1.  备份/etc/docker/daemon.json，完成后，执行以下命令更新/etc/docker/daemon.json。

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

2.  执行以下命令，重启Docker和Kubelet。

    ```
    service kubelet stop
    Redirecting to /bin/systemctl stop kubelet.service
    service docker restart
    Redirecting to /bin/systemctl restart docker.service
    service kubelet start
    Redirecting to /bin/systemctl start kubelet.service
    ```

3.  执行以下命令，确认Docker的Cgroup Driver的类型为systemd。

    ```
    docker info | grep -i cgroup
    Cgroup Driver: systemd
    ```


## 如何更改ACK集群中Worker节点的主机名称？

集群创建完成后，不支持自定义Worker节点的主机名称，但是您可以通过节点池的节点命名规则来修改Worker节点的主机名称。

**说明：** 创建集群时，您可以在**自定义节点名称**参数中定义Worker节点的主机名称。具体操作，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。

1.  移除节点。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在控制台左侧导航栏中，单击**集群**。

    3.  在集群管理页左侧导航栏中，选择**节点管理** \> **节点**。

    4.  在**节点**页面单击目标节点右侧**操作**列下的**更多** \> **移除**。

    5.  在弹出的对话框中选中**我已了解上述说明，确认移除节**点，然后单击**确定**。

2.  将移除的节点再添加到节点池。具体操作，请参见[手动添加节点](/cn.zh-CN/Kubernetes集群用户指南/节点与节点池/节点/添加已有节点.md)。

    添加的节点将根据节点池的节点命名规则进行命名。


