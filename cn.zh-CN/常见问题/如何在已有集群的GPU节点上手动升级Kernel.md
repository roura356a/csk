# 如何在已有集群的GPU节点上手动升级Kernel {#concept_943869 .concept}

本文介绍如何在已有集群的GPU节点上手动升级Kernel。

## 前提条件 {#section_jso_htc_po8 .section}

当前kernel版本低于`3.10.0-957.21.3`。

## 操作步骤 {#section_wqo_whb_xst .section}

1.  [通过 kubectl 连接 Kubernetes 集群](../../../../cn.zh-CN/Kubernetes集群用户指南/集群管理/管理与访问集群/通过 kubectl 连接 Kubernetes 集群.md#)。
2.  将GPU节点设置为不可调度（本例以节点 cn-beijing.i-2ze19qyi8votgjz12345为例）。

    ``` {#codeblock_szr_zge_wui}
    kubectl cordon cn-beijing.i-2ze19qyi8votgjz12345
    
    node/cn-beijing.i-2ze19qyi8votgjz12345 already cordoned
    ```

3.  将要升级驱动的GPU节点进行排水。

    ``` {#codeblock_l8l_2di_gkf}
    # kubectl drain cn-beijing.i-2ze19qyi8votgjz12345 --grace-period=120 --ignore-daemonsets=true
    
    node/cn-beijing.i-2ze19qyi8votgjz12345 cordoned
    WARNING: Ignoring DaemonSet-managed pods: flexvolume-9scb4, kube-flannel-ds-r2qmh, kube-proxy-worker-l62sf, logtail-ds-f9vbg
    pod/nginx-ingress-controller-78d847fb96-5fkkw evicted
    ```

4.  卸载当前的 nvidia-driver。

    **说明：** 本步骤中卸载的是版本为384.111的驱动包，如果您的驱动版本不是384.111，则需要Nvidia的官网下载对应的驱动安装包，并将本步骤中的`384.111`替换成您实际的版本。

    1.  您需要登录到该GPU节点，通过`nvidia-smi`查看驱动版本。

        ``` {#codeblock_2oi_z3e_exp}
        # nvidia-smi -a | grep 'Driver Version'
        Driver Version                      : 384.111
        ```

    2.  下载Nvidia驱动安装包。

        ``` {#codeblock_8lv_1ml_6ai}
        cd /tmp/
        curl -O https://cn.download.nvidia.cn/tesla/384.111/NVIDIA-Linux-x86_64-384.111.run
        ```

        **说明：** 需要在安装包中卸载Nvidia。

    3.  卸载当前Nvidia驱动。

        ``` {#codeblock_7ie_03a_ydu}
        chmod u+x NVIDIA-Linux-x86_64-384.111.run
        ./NVIDIA-Linux-x86_64-384.111.run --uninstall -a -s -q
        ```

5.  升级Kernel。

    ``` {#codeblock_hp2_q6g_jze}
    yum clean all && yum makecache
    yum update kernel  -y
    ```

6.  重启GPU机器。

    ``` {#codeblock_lle_d9k_ena}
    reboot
    ```

7.  重新登录GPU节点，安装对应的kernel devel。

    ``` {#codeblock_bhz_e2o_3jv}
    yum install -y kernel-devel-$(uname -r)
    ```

8.  请到Nvidia官网下载和安装您需要的nvidia驱动， 本文以410.79为例。

    ``` {#codeblock_kt9_2mt_yoz}
    cd /tmp/
    curl -O https://cn.download.nvidia.cn/tesla/410.79/NVIDIA-Linux-x86_64-410.79.run
    chmod u+x NVIDIA-Linux-x86_64-410.79.run
    sh ./NVIDIA-Linux-x86_64-410.79.run -a -s -q
    
    # warm up GPU
    nvidia-smi -pm 1 || true
    nvidia-smi -acp 0 || true
    nvidia-smi --auto-boost-default=0 || true
    nvidia-smi --auto-boost-permission=0 || true
    nvidia-modprobe -u -c=0 -m || true
    ```

9.  查看/etc/rc.d/rc.local，确认其中是否包含下列配置，如果没有请手动加上。

    ``` {#codeblock_xgc_15t_9w7}
    nvidia-smi -pm 1 || true
    nvidia-smi -acp 0 || true
    nvidia-smi --auto-boost-default=0 || true
    nvidia-smi --auto-boost-permission=0 || true
    nvidia-modprobe -u -c=0 -m || true
    ```

10. 重启kubelet和docker。

    ``` {#codeblock_8b3_7vm_40l}
    service kubelet stop
    service docker restart
    service kubelet start
    ```

11. 将这个GPU节点重新设置为可调度。

    ``` {#codeblock_es3_rxz_j4w}
    # kubectl uncordon cn-beijing.i-2ze19qyi8votgjz12345
    
    node/cn-beijing.i-2ze19qyi8votgjz12345 already uncordoned
    ```

12. 在GPU节点上的device plugin pod验证版本。

    ``` {#codeblock_136_m1g_h95}
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

    **说明：** 如果通过`docker ps`命令，发现GPU节点的没有容器被启动，请参见[修复GPU节点容器启动问题](cn.zh-CN/常见问题/修复GPU节点容器启动问题.md#)。


