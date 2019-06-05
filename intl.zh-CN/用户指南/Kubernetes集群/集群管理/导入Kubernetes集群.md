# 导入Kubernetes集群 {#concept_473355 .concept}

您可以通过容器服务控制台导入现有的Kubernetes集群，并通过容器服务控制台进行管理。

## 前提条件 {#section_7ek_yiv_lew .section}

-   您需要开通容器服务、弹性伸缩（ESS）服务和访问控制（RAM）服务。

    登录 [容器服务管理控制台](https://cs.console.aliyun.com/) 、 [RAM 管理控制台](https://ram.console.aliyun.com/) 和 [弹性伸缩控制台](https://essnew.console.aliyun.com) 开通相应的服务。

-   修改Kubernetes集群仍然必须在原有的集群中进行完成，包括添加与删除节点、升级Kubernetes集群版本以及更改Kubernetes组件参数等。
-   该功能目前只针对白名单客户开放。

## 操作步骤 {#section_o03_hks_j9u .section}

1.  创建导入集群。

    1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com)。
    2.  在 Kubernetes 菜单下，单击左侧导航栏的**集群** \> **集群**，进入集群列表页面。
    3.  单击页面右上角的创建**Kubernetes 集群**，在弹出的选择集群模板中，选择**导入已有Kubernetes集群**，单击**创建**。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/155970577248661_zh-CN.png)

        默认进入**导入已有 Kubernetes 集群**配置页面。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/155970577248722_zh-CN.png)

    4.  填写集群的名称。

        集群名称应包含1-63个字符，可包含数字、汉字、英文字符或连字符（-）。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/155970577248611_zh-CN.png)

    5.  选择集群所在的地域和可用区。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/155970577248614_zh-CN.png)

    6.  设置集群的网络。Kubernetes 集群仅支持专有网络。

        您可以在已有 VPC 列表中选择所需的 VPC 和交换机。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/155970577248615_zh-CN.png)

    7.  设置是否启用绑定EIP。

        勾选此选项，会在集群中自动绑定EIP，用于建立集群链接。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/155970577248725_zh-CN.png)

    8.  设置是否启用日志服务，您可使用已有Project或新建一个Project。

        勾选**使用日志服务**，会在集群中自动配置日志服务插件。创建应用时，您可通过简单配置，快速使用日志服务，详情参见[使用日志服务进行Kubernetes日志采集](intl.zh-CN/用户指南/Kubernetes集群/日志管理/使用日志服务进行Kubernetes日志采集.md#)。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15597057729244_zh-CN.png)

    9.  为集群绑定标签。

        输入键和对应的值，单击**添加**。

        **说明：** 

        -   键是必需的，而 值 是可选的，可以不填写。
        -   键 不能是 aliyun、http:// 、https:// 开头的字符串，不区分大小写，最多 64 个字符。
        -   值 不能是 http:// 或 https://，可以为空，不区分大小写，最多 128 个字符。
        -   同一个资源，标签键不能重复，相同标签键（Key）的标签会被覆盖。
        -   如果一个资源已经绑定了 20 个标签，已有标签和新建标签会失效，您需要解绑部分标签后才能再绑定新的标签。
    10. 单击**创建集群**，启动部署。
    **预期结果**

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/155970577248728_zh-CN.png)

2.  将目标集群导入新集群中。
    1.  在新集群**test-external-cluster1**右侧单击**管理**，进入**基本信息**页面。
    2.  在**集群资源**区域单击**集群导入代理配置**的页签。
    3.  单击**复制**，将以下yaml文件内容拷贝到agent.yaml文件中，并执行`kubectl apply -f agent.yaml`命令。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/155970577248732_zh-CN.png)

    4.  在目标集群中执行`kubectl get all -n ack-system`命令，查看代理运行状况。

        ``` {#codeblock_1qu_7ei_pbr}
        NAME                                     READY   STATUS    RESTARTS   AGE
        pod/ack-cluster-agent-655b75c987-dwp6b   1/1     Running   0          9s
        
        NAME                                DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
        deployment.apps/ack-cluster-agent   1         1         1            1           26m
        
        NAME                                           DESIRED   CURRENT   READY   AGE
        replicaset.apps/ack-cluster-agent-655b75c987   1         1         1       26m
        ```

    5.  导入成功后，您可以在容器服务管理控制台的 Kubernetes 集群列表页面，看到新集群的状态为运行中。

        在集群列表页面中，找到新集群**test-external-cluster1**，单击操作列中的**管理**，查看新集群的基本信息和连接信息。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/155970577248729_zh-CN.png)


## 执行结果 {#section_fae_ze8_1f1 .section}

您可以[通过 kubectl 连接 Kubernetes 集群](intl.zh-CN/用户指南/Kubernetes集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)，执行`kubectl get node`命令，查看新集群**test-external-cluster1**的节点信息。此时，您可以使用该kubeconfig连接远程的被接入集群，进行应用负载的部署。

**说明：** 

其中，**API Server 公网连接端点**：Kubernetes 的 API server 对公网提供服务的地址和端口，可以通过此服务在用户终端使用 kubectl 等工具管理集群。

