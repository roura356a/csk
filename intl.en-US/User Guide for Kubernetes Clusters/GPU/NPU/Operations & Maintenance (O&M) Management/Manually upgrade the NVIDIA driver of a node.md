---
keyword: [upgrade the NVIDIA driver, manual upgrade]
---

# Manually upgrade the NVIDIA driver of a node

If your Compute Unified Device Architecture \(CUDA\) library requires a later version of the NVIDIA driver, you must upgrade the NVIDIA driver of the node. This topic describes how to upgrade the NVIDIA driver of a node.

## Prerequisites

[t16645.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md)

## Procedure

1.  Run the following command to set the GPU-accelerated node that you want to manage to unschedulable:

    ```
    kubectl cordon <$Node\_ID\>
    ```

    **Note:** Replace <$Node\_ID\> with the node ID.

    Expected output:

    ```
    node/<$Node\_ID\> already cordoned
    ```

2.  Run the following command to remove the GPU-accelerated node that you want to manage from the cluster:

    ```
    kubectl drain <$Node\_ID\> --grace-period=120 --ignore-daemonsets=true   # Configure a grace period of 120 seconds to gracefully stop the workloads on the node. 
    ```

    Expected output:

    ```
    node/cn-beijing.<$Node\_ID\> cordoned
    WARNING: Ignoring DaemonSet-managed pods: flexvolume-9scb4, kube-flannel-ds-r2qmh, kube-proxy-worker-l62sf, logtail-ds-f9vbg
    pod/nginx-ingress-controller-78d847fb96-5fkkw evicted
    ```

3.  Uninstall the NVIDIA driver from the GPU-accelerated node.

    **Note:** In this step, the NVIDIA driver of version 418.87.01 is uninstalled. If the version of the NVIDIA driver that you want to uninstall is not 418.87.01, go to the NVIDIA official website and download the installation package of the NVIDIA driver of the required version. Then, replace the installation package of version 418.87.01 with the downloaded installation package.

    1.  Log on to the GPU-accelerated node and run the following command to query the NVIDIA driver version.

        **Note:** For more information about how to log on to a GPU-accelerated node, see [Connect to a Linux instance by using password authentication](/intl.en-US/Instance/Connect to instances/Connect to an instance by using VNC/Connect to a Linux instance by using password authentication.md) and [Connect to a Windows instance by using password authentication](/intl.en-US/Instance/Connect to instances/Connect to an instance by using VNC/Connect to a Windows instance by using password authentication.md).

        ```
        nvidia-smi -a | grep 'Driver Version'
        ```

        Expected output:

        ```
        Driver Version                      : 418.87.01
        ```

    2.  Run the following command to download the installation package of the NVIDIA driver of the version that you want to uninstall:

        ```
        cd /tmp/
        wget https://us.download.nvidia.com/tesla/418.87/NVIDIA-Linux-x86_64-418.87.01.run
        ```

    3.  Run the following command to uninstall the current NVIDIA driver:

        ```
        chmod u+x NVIDIA-Linux-x86_64-418.87.01.run
        ./NVIDIA-Linux-x86_64-418.87.01.run --uninstall -a -s -q
        ```

4.  Run the following command to restart the GPU-accelerated node:

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

6.  Refer to the following commands to set the parameters based on your business requirements:

    ```
    nvidia-smi -pm 1 || true                            # Enable the Persistence mode. 
    nvidia-smi -acp 0 || true                           # Change the permission requirements to UNRESTRICTED. 
    nvidia-smi --auto-boost-default=0 || true           # Disable the default auto boost mode. A value of 0 indicates that the default auto boost mode is disabled. 
    nvidia-smi --auto-boost-permission=0 || true        # Allow non-administrator users to control the auto boost mode. A value of 0 indicates allowance. A value of 1 indicates disallowance. 
    nvidia-modprobe -u -c=0 -m || true                  # Load the NVIDIA Unified Memory kernel module instead of the NVIDIA kernel module. Create the NVIDIA device file with the given number. 
    ```

7.  To start the NVIDIA driver at startup, check the /etc/rc.d/rc.local file and make sure that the following content is added to the file:

    ```
    nvidia-smi -pm 1 || truenvidia-smi -acp 0 || truenvidia-smi --auto-boost-default=0 || truenvidia-smi --auto-boost-permission=0 || truenvidia-modprobe -u -c=0 -m || true
    ```

8.  Run the following command to restart kubelet and Docker:

    ```
    systemctl restart docker && systemctl restart kubelet
    ```

9.  Run the following command to set the GPU-accelerated node to schedulable:

    ```
    kubectl uncordon <$Node\_ID\>
    ```

    Expected output:

    ```
    node/<$Node\_ID\> already uncordoned
    ```

10. Run the following command on the device-plugin pod of the GPU-accelerated node to query the driver version:

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

    The preceding output indicates that the NVIDIA driver is upgraded to the required version.

    If the NVIDIA driver is not upgraded to the required version, run the `docker ps` command to check whether containers on the node have started. For more information about the issues related to container startup, see [Failed to start a container on the GPU node]().


