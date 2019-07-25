# 接入外部Kubernetes集群 {#task_skz_qwk_qfb .task}

您可以通过容器服务控制台接入现有的外部Kubernetes集群，并通过容器服务控制台进行管理。

-   您需要开通容器服务、弹性伸缩（ESS）服务和访问控制（RAM）服务。

    登录 [容器服务管理控制台](https://cs.console.aliyun.com/) 、 [RAM 管理控制台](https://ram.console.aliyun.com/) 和 [弹性伸缩控制台](https://essnew.console.aliyun.com) 开通相应的服务。

-   修改Kubernetes集群仍然必须在原有的集群中进行完成，包括添加与删除节点、升级Kubernetes集群版本以及更改Kubernetes组件参数等。
-   该功能目前只针对白名单客户开放。

1.  创建接入集群。 
    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
    2.  在 Kubernetes 菜单下，单击左侧导航栏的**集群** \> **集群**，进入集群列表页面。
    3.  单击页面右上角的创建**Kubernetes 集群**，在弹出的选择集群模板中，选择**接入已有集群**，单击**创建**。 默认进入**接入已有集群**配置页面。

        ![接入已有集群](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/156402432648722_zh-CN.png)

    4.  填写集群的名称。 集群名称应包含1-63个字符，可包含数字、汉字、英文字符或连字符（-）。

        ![集群命名](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/156402432648611_zh-CN.png)

    5.  选择集群所在的地域和可用区。![地域和可用区](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/156402432648614_zh-CN.png)


    6.  设置集群的网络。Kubernetes 集群仅支持专有网络。 您可以在已有 VPC 列表中选择所需的 VPC 和交换机。

        ![专有网络](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/156402432648615_zh-CN.png)

    7.  设置是否启用绑定EIP。 勾选此选项，会在集群中自动绑定EIP，用于建立集群链接。

        ![绑定EIP](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/156402432648725_zh-CN.png)

    8.  为集群绑定标签。 输入键和对应的值，单击**添加**。

        **说明：** 

        -   键是必需的，而 值 是可选的，可以不填写。
        -   键 不能是 aliyun、http:// 、https:// 开头的字符串，不区分大小写，最多 64 个字符。
        -   值 不能是 http:// 或 https://，可以为空，不区分大小写，最多 128 个字符。
        -   同一个资源，标签键不能重复，相同标签键（Key）的标签会被覆盖。
        -   如果一个资源已经绑定了 20 个标签，已有标签和新建标签会失效，您需要解绑部分标签后才能再绑定新的标签。
    9.  单击**创建集群**，启动部署。 您可以在集群列表 ，看到新集群。

        ![集群列表](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/156402432648728_zh-CN.png)

2.  将目标集群接入新集群中。 
    1.  在新集群（本例中为**test-external-cluster1**）右侧单击**管理**，进入**基本信息**页面。
    2.  在**集群接入代理配置**区域单击**复制**，将以下yaml文件内容拷贝到agent.yaml文件中，并执行`kubectl apply -f agent.yaml`命令。 

        ![导入配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/156402432748732_zh-CN.png)

    3.  在目标集群中执行`kubectl get all -n ack-system`命令，查看代理运行状况。 

        ``` {#codeblock_57v_51x_t4y}
        NAME                                     READY   STATUS    RESTARTS   AGE
        pod/ack-cluster-agent-655b75c987-dwp6b   1/1     Running   0          9s
        
        NAME                                DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
        deployment.apps/ack-cluster-agent   1         1         1            1           26m
        
        NAME                                           DESIRED   CURRENT   READY   AGE
        replicaset.apps/ack-cluster-agent-655b75c987   1         1         1       26m
        ```

        接入成功后，您可以在容器服务管理控制台的 Kubernetes 集群列表页面，看到新集群的状态为运行中。

        ![集群列表2](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/156402432752710_zh-CN.png)


在集群列表页面中，找到新集群**test-external-cluster1**，单击操作列中的**管理**，查看新集群的基本信息和连接信息。

![基本信息](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/156402432752922_zh-CN.png)

您可以[通过 kubectl 连接 Kubernetes 集群](intl.zh-CN/用户指南/Kubernetes集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)，执行`kubectl get node`查看新集群**test-external-cluster1**的节点信息。此时，您可以使用该kubeconfig连接远程的被接入集群，进行应用负载的部署。

![pod](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/156402432748729_zh-CN.png)

您也可以在该接入的集群中通过使用Helm来发布管理应用。

![helm](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/380912/156402432752712_zh-CN.png)

