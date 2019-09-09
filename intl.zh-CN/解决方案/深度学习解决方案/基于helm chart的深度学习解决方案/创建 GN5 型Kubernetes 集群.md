# 创建 GN5 型Kubernetes 集群 {#task_1958341 .task}

下面将介绍如何在阿里云容器服务上创建 Kubernetes GPU 集群。

您需要申请一个按量付费的GPU计算型gn5。请 [提交 ECS 工单](https://selfservice.console.aliyun.com/ticket/scene/ecs/%E4%BA%91%E6%9C%8D%E5%8A%A1%E5%99%A8%20ECS/detail)申请开通。

Kubernetes 深度学习解决方案支持使用云服务器 ECS 的 Kubernetes 集群或者 GPU 服务器 Kubernetes 集群。本文档以 GPU 服务器容器集群为例进行说明。

**说明：** 有关如何创建 ECS 容器集群，参见[创建Kubernetes 集群](../cn.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes 集群.md#)。

而 Kubernetes 全新的 GPU 调度方案基于 Nvidia 官方的设备插件和 nvidia-container-runtime，和之前社区方案相比，最终用户所要做的配置更少。

基于该方案，客户可以将应用程序利用容器技术构建镜像，结合 Kubernetes+GPU 运行机器学习，图像处理等高运算密度等任务，无需安装 nvidia driver 和 CUDA，就能实现一键部署和弹性扩缩容等功能。

## 使用限制 {#section_up7_ext_t4s .section}

-   目前，gn5 型 GPU 云服务器只支持专有网络（VPC）。
-   用户账户需有 100 元的余额并通过实名认证，否则无法创建按量付费的 ECS 实例和负载均衡。
-   Kubernetes 深度学习解决方案要求 Kubernetes 集群的版本在 1.9.3 及以上。

## 操作步骤 {#section_ap8_t3r_c94 .section}

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏的**集群** \> **集群**，进入集群列表页面。
3.  单击页面右上角的 **创建 Kubernetes 集群**。 

    ![集群列表](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16630/15680173969083_zh-CN.png)

4.  配置集群基本信息。 

    ![配置基本信息](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16630/15680173969085_zh-CN.png)

5.  为 worker 节点选择实例系列，本例中选择 GPU 计算型 gn5。 

    ![master配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16630/15680173969089_zh-CN.png)

6.  勾选开放公网 SSH 登录，这样就可以通过 SSH 登录 Kubernetes 的 Master 节点。 

    ![SSH登录](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16630/15680173969090_zh-CN.png)

    **说明：** 其他集群参数的详细配置信息，请参见 [创建Kubernetes 集群](../cn.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes 集群.md#)。

7.  完成配置后，单击 **创建集群**，等待一段时间，新建的 GPU 集群会出现在集群列表中。 

    ![管理集群](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16630/15680173979091_zh-CN.png)

8.  单击集群右侧的 **管理**，进入集群基本信息页面，查看 Master节点 SSH 连接地址。 

    ![集群详情](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16630/15680173979092_zh-CN.png)

9.  SSH 登录到 Master节点，可通过执行如下命令，查找集群下的 GPU 节点。 

    ``` {#codeblock_vla_oel_pt9}
     $ kubectl get nodes -l 'aliyun.accelerator/nvidia_name' --show-labels
     ...                               
     NAME                                 STATUS    ROLES     AGE       VERSION   LABELS
     cn-hangzhou.i-bp12xvjjwqe6j7nca2q8   Ready     <none>    1h        v1.9.3    aliyun.accelerator/nvidia_count=1,aliyun.accelerator/nvidia_mem=16276MiB,aliyun.accelerator/nvidia_name=Tesla-P100-PCIE-16GB，..
    ```

10. 查看 GPU 节点的详细状态信息。 

    ``` {#codeblock_gp2_7t4_be9}
    $ kubectl get node ${node_name} -o=yaml
     ...
     status:
       addresses:
       - address: 172.16.166.23
         type: InternalIP
       allocatable:
         cpu: "8"
         memory: 61578152Ki
         nvidia.com/gpu: "1"
         pods: "110"
       capacity:
         cpu: "8"
         memory: 61680552Ki
         nvidia.com/gpu: "1"
         pods: "110"
     ...
    ```


至此，创建的 Kubernetes 的 GPU 集群已经创建完毕。

