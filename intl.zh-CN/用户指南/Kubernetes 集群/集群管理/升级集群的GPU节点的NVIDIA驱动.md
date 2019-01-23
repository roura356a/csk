# 升级集群的GPU节点的NVIDIA驱动 {#concept_ndf_t2p_mgb .concept}

本文介绍在Kubernetes集群的GPU节点有业务运行，及集群刚刚创建GPU节点没有业务运行两种场景下，如何升级集群GPU节点的NVIDIA驱动。

## 前提条件 {#section_rxk_1fp_mgb .section}

-   您已经成功创建一个Kubernetes GPU集群，参见[Kubernetes GPU集群支持GPU调度](intl.zh-CN/用户指南/Kubernetes 集群/集群管理/Kubernetes GPU集群支持GPU调度.md#)。
-   您可以通过kubectl连接到Kubernetes GPU集群，参见[通过 kubectl 连接 Kubernetes 集群](intl.zh-CN/用户指南/Kubernetes 集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)。

## 升级已有业务运行的GPU节点的驱动 {#section_fyj_pfp_mgb .section}

1.  执行以下命令，把待升级驱动的GPU节点设置为不可调度。

    ```
    kubectl cordon node-name
    ```

    **说明：** 

    -   仅支持升级Worker节点的NVIDIA驱动。
    -   node-name格式为your-region-name.node-id。

        -   your-region-name为您集群所在的地域名称。
        -   node-id为节点所在的ECS实例ID。
        可执行以下命令查看node-name。

        ```
        kubectl get node
        ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/112078/154821275637636_zh-CN.png)

2.  执行以下命令，把步骤1节点上的Pod转移到其他节点。

    ```
    kubectl drain node-name --grace-period=120 --ignore-daemonsets=true
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/112078/154821275637635_zh-CN.png)

3.  执行以下命令，登录步骤1的节点。

    ```
    ssh root@xxx.xxx.x.xx
    ```

4.  在待升级驱动的节点上，执行以下命令，查看驱动版本。

    ```
    nvidia-smi
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/112078/154821275637639_zh-CN.png)

5.  执行以下命令，删除已有的驱动。

    **说明：** 

    -   如果您驱动的版本是384.111，请按照以下步骤执行。
    -   如果您驱动的版本不是384.111，请到NVIDIA官网下载对应版本的驱动包后，按照以下步骤执行卸载。
    ```
    cd /tmp
    ```

    ```
    curl -O https://cn.download.nvidia.cn/tesla/384.111/NVIDIA-Linux-x86_64-384.111.run
    ```

    ```
    chmod u+x NVIDIA-Linux-x86_64-384.111.run
    ```

    ```
    ./NVIDIA-Linux-x86_64-384.111.run --uninstall -a -s -q
    ```

6.  执行以下命令，重启待升级驱动的节点。

    ```
    reboot
    ```

7.  请到官网下载需要升级到的驱动，本文以410.79版本为例。
8.  执行以下命令，在步骤7下载驱动的路径安装NVIDIA驱动。

    ```
    sh ./NVIDIA-Linux-x86_64-410.79.run -a -s -q
    ```

9.  执行以下命令，在NVIDIA驱动中增加以下配置。

    ```
    nvidia-smi -pm 1 || true
    ```

    ```
    nvidia-smi -acp 0 || true
    ```

10. 执行以下命令，更新device-plugin。

    ```
    mv /etc/kubernetes/manifests/nvidia-device-plugin.yml /
    ```

    ```
    mv /nvidia-device-plugin.yml /etc/kubernetes/manifests/
    ```

11. 在Master节点的任意路径下执行以下命令，将此节点设置为可调度。

    ```
    kubectl uncordon node-name
    ```


**执行结果**

在Master节点执行以下命令，在升级驱动的GPU节点上验证升级后的驱动版本，可看到该GPU节点上NVIDIA驱动的版本为410.79，说明该节点的驱动已升级成功。

**说明：** 请替换您实际的node-name。

```
kubectl exec -n kube-system -t nvidia-device-plugin-node-name nvidia-smi
```

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/112078/154821275637730_zh-CN.png)

## 集群刚刚创建，升级没有业务运行的GPU节点的驱动 {#section_z1n_btj_ngb .section}

1.  执行以下命令，登录待升级驱动的GPU节点。

    ```
    ssh root@xxx.xxx.x.xx
    ```

2.  在待升级驱动的节点上，执行以下命令，查看驱动版本。

    ```
    nvidia-smi
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/112078/154821275637639_zh-CN.png)

3.  执行以下命令，删除已有的驱动。

    **说明：** 

    -   如果您驱动的版本是384.111，请按照以下步骤执行。
    -   如果您驱动的版本不是384.111，请到NVIDIA官网下载对应版本的驱动包后，按照以下步骤执行卸载。
    ```
    cd /tmp
    ```

    ```
    curl -O https://cn.download.nvidia.cn/tesla/384.111/NVIDIA-Linux-x86_64-384.111.run
    ```

    ```
    chmod u+x NVIDIA-Linux-x86_64-384.111.run
    ```

    ```
    ./NVIDIA-Linux-x86_64-384.111.run --uninstall -a -s -q
    ```

4.  执行以下命令，重启待升级驱动的节点。

    ```
    reboot
    ```

5.  请到官网下载需要升级到的驱动，本文以410.79版本为例。
6.  执行以下命令，在步骤5下载驱动的路径安装NVIDIA驱动。

    ```
    sh ./NVIDIA-Linux-x86_64-410.79.run -a -s -q
    ```

7.  执行以下命令，在NVIDIA驱动中增加以下配置。

    ```
    nvidia-smi -pm 1 || true
    ```

    ```
    nvidia-smi -acp 0 || true
    ```


**执行结果**

在Master节点执行以下命令，在升级驱动的GPU节点上验证升级后的驱动版本，可看到该GPU节点上NVIDIA驱动的版本为410.79，说明该节点的驱动已升级成功。

**说明：** 请替换您实际的node-name。

```
kubectl exec -n kube-system -t nvidia-device-plugin-node-name nvidia-smi
```

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/112078/154821275637730_zh-CN.png)

