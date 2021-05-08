---
keyword: [upgrade the NVIDIA driver, manual upgrade]
---

# Manually upgrade the NVIDIA driver for a node

If your CUDA Toolkit requires a higher version of the NVIDIA driver, you must upgrade the NVIDIA driver. This topic describes how to manually upgrade the NVIDIA driver for a node.

## Prerequisites

[Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md)

## Procedure

1.  Run the following command to set the GPU node that you want to upgrade to unschedulable:

    ```
    kubectl cordon <$Node\_ID\>
    ```

    **Note:** Replace <$Node\_ID\> with the ID of the node.

    Expected output:

    ```
    node/<$Node\_ID\> already cordoned
    ```

2.  Run the following command to remove the GPU node that you want to upgrade from the cluster:

    ```
    kubectl drain <$Node\_ID\> --grace-period=120 --ignore-daemonsets=true
    ```

    Expected output:

    ```
    node/cn-beijing.<$Node\_ID\> cordoned
    WARNING: Ignoring DaemonSet-managed pods: flexvolume-9scb4, kube-flannel-ds-r2qmh, kube-proxy-worker-l62sf, logtail-ds-f9vbg
    pod/nginx-ingress-controller-78d847fb96-5fkkw evicted
    ```

3.  Uninstall the NVIDIA driver from the GPU node.

    **Note:** In this step, the NVIDIA driver of version 418.87.01 is uninstalled. If the version of the NVIDIA driver that you want to uninstall is not 418.87.01, go to the NVIDIA official website and download the installation package of the NVIDIA driver. You must replace the installation package of version 418.87.01 with your downloaded installation package.

    1.  Log on to the GPU node and run the following command to query the NVIDIA driver version.

        **Note:** For more information about how to log on to a GPU node, see [Connect to a Linux instance by using password authentication](/intl.en-US/Instance/Connect to instances/Connect to an instance by using VNC/Connect to a Linux instance by using password authentication.md) and [Connect to a Windows instance by using password authentication](/intl.en-US/Instance/Connect to instances/Connect to an instance by using VNC/Connect to a Windows instance by using password authentication.md).

        ```
        nvidia-smi -a | grep 'Driver Version'
        ```

        Expected output:

        ```
        Driver Version                      : 418.87.01
        ```

    2.  Run the following command to download the installation package of the NVIDIA driver that you want to uninstall:

        ```
        cd /tmp/
        wget https://us.download.nvidia.com/tesla/418.87/NVIDIA-Linux-x86_64-418.87.01.run
        ```

    3.  Run the following command to uninstall the current NVIDIA driver:

        ```
        chmod u+x NVIDIA-Linux-x86_64-418.87.01.run
        ./NVIDIA-Linux-x86_64-418.87.01.run --uninstall -a -s -q
        ```

4.  Run the following command to restart the GPU node:

    ```
    reboot
    ```

5.  Run the following command to download and install the NVIDIA driver version to which you want to upgrade. In this example, version 418.181.07 is used.

    ```
    cd /tmp/
    wget https://us.download.nvidia.com/tesla/418.181.07/NVIDIA-Linux-x86_64-418.181.07.run
    chmod u+x NVIDIA-Linux-x86_64-418.181.07.run
    sh ./NVIDIA-Linux-x86_64-418.181.07.run -a -s -q
    ```

6.  Refer to the following commands to configure the parameters based on your business requirements:

    ```
    nvidia-smi -pm 1 || true
    nvidia-smi -acp 0 || true
    nvidia-smi --auto-boost-default=0 || true
    nvidia-smi --auto-boost-permission=0 || true
    nvidia-modprobe -u -c=0 -m || true
    ```

7.  To start the NVIDIA driver at startup, check the /etc/rc.d/rc.local file and make sure that the following content is added to the file.

    ```
    nvidia-smi -pm 1 || truenvidia-smi -acp 0 || truenvidia-smi --auto-boost-default=0 || truenvidia-smi --auto-boost-permission=0 || truenvidia-modprobe -u -c=0 -m || true
    ```

8.  Run the following command to restart kubelet and the Docker runtime:

    ```
    systemctl restart docker && systemctl restart kubelet
    ```

9.  Run the following command to set the GPU node to schedulable:

    ```
    kubectl uncordon <$Node\_ID\>
    ```

    Expected output:

    ```
    node/<$Node\_ID\> already uncordoned
    ```

10. Run the following command on the device-plugin pod of the GPU node to check the driver version:

    ```
    kubectl exec -n kube-system -t nvidia-device-plugin-<$Node\_ID\> nvidia-smi
    ```

    Expected output:

    ```
    Thu Jan 17 00:33:27 2019
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 418.181.07   Driver Version: 418.181.07   CUDA Version: N/A      |
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
    +-----------------------------------------------------------------------------
    ```

    The preceding output indicates that the NVIDIA driver is upgraded.

    If the NVIDIA driver is not upgraded, you can run the `docker ps` command to check whether containers are started on the GPU node. For more information about how to troubleshoot container startup issues, see [Failed to start a container on the GPU node]().


