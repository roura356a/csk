# 注册外部Kubernetes集群

您可以通过容器服务控制台注册现有的外部Kubernetes集群，并通过容器服务控制台进行管理。

-   登录[RAM管理控制台](https://ram.console.aliyun.com/)和[弹性伸缩控制台](https://essnew.console.aliyun.com)开通相应的服务。

-   修改Kubernetes集群仍然必须在原有的集群中进行完成，包括添加与删除节点、升级Kubernetes集群版本以及更改Kubernetes组件参数等。

1.  创建接入集群。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在控制台左侧导航栏中，单击**集群**。

    3.  在集群列表页面中，单击页面右上角的**创建集群**。

    4.  在注册集群页签，完成创建集群配置项。

        |配置项|描述|
        |---|--|
        |**集群名称**|填写集群的名称。

**说明：** 集群名称应包含1~63个字符，可包含数字、汉字、英文字符或短划线（-）。 |
        |**资源组**|将鼠标悬浮于页面上方的**账号全部资源**，选择集群所在的资源组。这里显示选择的资源组。

![资源组](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0706659951/p127165.png) |
        |**地域**|选择集群所在的地域。 |
        |**可用区**|选择集群所在的可用区。|
        |**专有网络**|您可以在已有VPC列表中选择所需的VPC和交换机。|
        |**API Server访问**|ACK默认为API Server创建一个内网SLB实例，您可修改SLB实例规格。更多信息，请参见[实例规格]()。

**说明：** 删除默认创建的SLB实例将会导致无法访问API Server。 |
        |**绑定EIP**|设置是否启用绑定EIP。选中此选项，会在集群中自动绑定EIP，用于建立集群链接。|
        |**安全组**|支持选择**自动创建普通安全组**、**自动创建企业级安全组**、**选择已有安全组**。有关安全组的详细内容，请参见[安全组概述](/intl.zh-CN/安全/安全组/安全组概述.md)。 |
        |**日志服务**|设置是否启用日志服务，您可使用已有Project或新建一个Project。勾选**使用日志服务**，会在集群中自动配置日志服务插件。创建应用时，您可通过简单配置，快速使用日志服务，详情请参见[通过日志服务采集Kubernetes容器日志](/intl.zh-CN/Kubernetes集群用户指南/可观测性/日志管理/通过日志服务采集Kubernetes容器日志.md)。 |
        |**集群删除保护**|设置是否启用集群删除保护。为防止通过控制台或API误释放集群。|
        |**标签**|为集群绑定标签。输入键和对应的值，单击**添加**。

**说明：**

        -   键是必需的，而值是可选的，可以不填写。
        -   键不能是aliyun、acs:、http://或https://开头的字符串，不区分大小写，最多64个字符。
        -   值不能是aliyun、acs:、http://或https://开头的字符串，可以为空，不区分大小写，最多128个字符。
        -   同一个资源，标签键不能重复，相同标签键（Key）的标签会被覆盖。
        -   如果一个资源已经绑定了20个标签，已有标签和新建标签会失效，您需要解绑部分标签后才能再绑定新的标签。 |
        |**服务协议**|创建集群前，需阅读并同意*服务条款*及*免责申明*。|

    5.  在页面右侧，单击**创建集群**，启动部署。

        您可以在集群列表，看到您创建的集群。

        ![集群列表](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6206659951/p48728.png)

2.  将目标集群注册至新集群中。

    1.  在新集群（本例中为**test-external-cluster1**）右侧单击**操作**列下的**详情**。

    2.  在**集群信息**页面单击**连接信息**页签。

    3.  在**集群导入代理配置**区域根据需要选择**公网**或者**内网**，然后单击右侧的**复制**，将公网或内网页签的内容拷贝到一个文件中，并执行kubectl命令，将目标集群注册至新集群中。

        例如，您可以新建agent.yaml文件，将以下内容拷贝到agent.yaml文件中，并在目标集群中执行`kubectl apply -f agent.yaml`命令。

        ![导入配置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6206659951/p48732.png)

    4.  在目标集群中执行`kubectl get all -n kube-system`命令，查看代理运行状况。

        返回结果如下：

        ```
        NAME                                     READY   STATUS    RESTARTS   AGE
        pod/ack-cluster-agent-655b75c987-dwp6b   1/1     Running   0          9s
        
        NAME                                DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
        deployment.apps/ack-cluster-agent   1         1         1            1           26m
        
        NAME                                           DESIRED   CURRENT   READY   AGE
        replicaset.apps/ack-cluster-agent-655b75c987   1         1         1       26m
        ```

        注册成功后，您可以在容器服务管理控制台的Kubernetes集群列表页面，看到该集群的状态为**运行中**。


在集群列表页面中，找到新集群**test-external-cluster1**，单击**操作**列下的**详情**，查看新集群的**基本信息**和**连接信息**。

执行`kubectl get node`查看新集群**test-external-cluster1**的节点信息。此时，您可以使用该kubeconfig连接远程的被注册集群，进行应用负载的部署。连接集群的具体操作步骤，请参见[通过kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群管理/连接集群/通过kubectl连接Kubernetes集群.md)。

您也可以通过集群管理页，选择**发布** \> **Helm**，在该注册的集群中使用Helm来发布管理应用。

**相关文档**  


[注册外部集群简介](/intl.zh-CN/Kubernetes集群用户指南/多云混合云管理/注册外部集群简介.md)

