---
keyword: [升级GPU节点驱动, 手动升级]
---

# 手动升级GPU节点驱动

如果您使用的CUDA库需要匹配更高版本的NVIDIA驱动，需要升级节点的NVIDIA驱动。本文介绍如何手动升级GPU节点驱动。

## 前提条件

[t16645.md\#](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl管理Kubernetes集群.md)

## 操作步骤

1.  执行以下命令，将待升级驱动的GPU节点设置为不可调度状态。

    ```
    kubectl cordon <$Node\_ID\>
    ```

    **说明：** <$Node\_ID\>为节点的ID。

    预期输出：

    ```
    node/<$Node\_ID\> already cordoned
    ```

2.  执行以下命令，将待升级驱动的GPU节点从集群中移除。

    ```
    kubectl drain <$Node\_ID\> --grace-period=120 --ignore-daemonsets=true   #设置120秒钟的宽限期限，让任务优雅的关闭。
    ```

    预期输出：

    ```
    node/cn-beijing.<$Node\_ID\> cordoned
    WARNING: Ignoring DaemonSet-managed pods: flexvolume-9scb4, kube-flannel-ds-r2qmh, kube-proxy-worker-l62sf, logtail-ds-f9vbg
    pod/nginx-ingress-controller-78d847fb96-5fkkw evicted
    ```

3.  卸载该GPU节点的NVIDIA驱动。

    **说明：** 本步骤中卸载的是418.87.01版本的驱动包，如果您的目标驱动版本不是418.87.01，则需要到NVIDIA的官网中下载对应的驱动安装包，并将本步骤中的418.87.01版本的驱动包替换成您实际的版本。

    1.  登录到该GPU节点，执行以下命令，查看驱动版本。

        **说明：** 关于如何登录到GPU节点，请参见[通过密码认证登录Linux实例](/intl.zh-CN/实例/连接实例/使用VNC连接实例/通过密码认证登录Linux实例.md)和[通过密码认证登录Windows实例](/intl.zh-CN/实例/连接实例/使用VNC连接实例/通过密码认证登录Windows实例.md)。

        ```
        nvidia-smi -a | grep 'Driver Version'
        ```

        预期输出：

        ```
        Driver Version                      : 418.87.01
        ```

    2.  执行以下命令，下载NVIDIA驱动安装包。

        ```
        cd /tmp/
        wget https://us.download.nvidia.com/tesla/418.87/NVIDIA-Linux-x86_64-418.87.01.run
        ```

    3.  执行以下命令，卸载当前NVIDIA驱动。

        ```
        chmod u+x NVIDIA-Linux-x86_64-418.87.01.run
        ./NVIDIA-Linux-x86_64-418.87.01.run --uninstall -a -s -q
        ```

4.  执行以下命令，重启GPU节点。

    ```
    reboot
    ```

5.  执行以下命令，从NVIDIA官网下载并安装您需要的NVIDIA驱动，本文以下载418.181.07版本为例。

    ```
    cd /tmp/
    wget https://us.download.nvidia.com/tesla/418.181.07/NVIDIA-Linux-x86_64-418.181.07.run
    chmod u+x NVIDIA-Linux-x86_64-418.181.07.run
    sh ./NVIDIA-Linux-x86_64-418.181.07.run -a -s -q
    ```

6.  参考以下命令，根据实际情况，添加优化参数。

    ```
    nvidia-smi -pm 1 || true                            # 代表启用Persistence模式。
    nvidia-smi -acp 0 || true                           # 切换权限要求为UNRESTRICTED。
    nvidia-smi --auto-boost-default=0 || true           # 不开启自动提升模式，0代表不启用。
    nvidia-smi --auto-boost-permission=0 || true        # 允许非管理员控制自动提升模式，0代表允许，1代表不允许。
    nvidia-modprobe -u -c=0 -m || true                  # 加载NVIDIA统一内存内核模块，而不是NVIDIA内核模块，且使用给定的编号创建NVIDIA设备文件。
    ```

7.  如果需要开机自启动NVIDIA驱动，请查看/etc/rc.d/rc.local文件，确认包含以下配置，如果没有请手动添加。

    ```
    nvidia-smi -pm 1 || truenvidia-smi -acp 0 || truenvidia-smi --auto-boost-default=0 || truenvidia-smi --auto-boost-permission=0 || truenvidia-modprobe -u -c=0 -m || true
    ```

8.  执行以下命令，重启Kubelet和Docker服务。

    ```
    systemctl restart docker && systemctl restart kubelet
    ```

9.  执行以下命令，将GPU节点重新设置为可调度状态。

    ```
    kubectl uncordon <$Node\_ID\>
    ```

    预期输出：

    ```
    node/<$Node\_ID\> already uncordoned
    ```

10. 执行以下命令，在GPU节点的device-plugin Pod上验证版本信息。

    ```
    kubectl exec -n kube-system -t nvidia-device-plugin-<$Node\_ID\> nvidia-smi
    ```

    预期输出：

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

    从上述输出信息，可以知道GPU节点的版本已成功升级到预期版本。

    如果没有升级到预期版本，您可以通过执行`docker ps`命令，查看GPU节点的容器是否启动。关于GPU节点容器启动问题，请参见[修复GPU节点容器启动问题]()。


