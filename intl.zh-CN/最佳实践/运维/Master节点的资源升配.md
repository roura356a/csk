# Master节点的资源升配 {#task_895194 .task}

Kubernetes 专有版集群的Master节点由用户自己管控，随着集群规模的不断扩大，用户可能会遇到Master节点的配置不足的情况，本文为您介绍如何一步步升级Master的配置。

您已经成功创建Kubernetes集群，参见[创建Kubernetes集群](../../../../intl.zh-CN/用户指南/Kubernetes集群/集群管理/创建Kubernetes集群.md#)。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com/)。
2.  在 Kubernetes 菜单下，选择**集群** \> **节点**，进入节点列表。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/725484/156394998250526_zh-CN.png)

3.  找到集群的三个master节点，单击master节点的实例ID，进入实例详情页面（本文档以master-01为例）。 

    此时，您可以看到实例规格等信息。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/725484/156394998350527_zh-CN.png)

4.  在实例的配置信息区域，单击**更改实例规格**。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/725484/156394998350517_zh-CN.png)

    如果**更改实例规格**为灰显，说明实例正在运行 ，您需要单击界面右上角的**停止**，停止运行该实例，再进行下一步。

5.  在弹出的调整规格页面，选择合适的规格，单击**确认调整**。节点规格请参见[选择Master节点规格](intl.zh-CN/最佳实践/集群/Kubernetes集群选型及高可靠推荐配置/ECS选型.md#section_g2v_jry_nfb)。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/725484/156394998350519_zh-CN.png)

6.  在弹出的调整规格成功页面，单击**确认**。 此时自动跳转到实例列表页面。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/725484/156394998350514_zh-CN.png)

    可以看到master-01节点的规格已经调整成功。

7.  勾选目标实例，单击**启动**，等待升配的master-01节点自动加入集群且状态变成**运行中**，如图所示。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/725484/156394998450530_zh-CN.png)

8.  重复步骤4-7，直至所有Master节点均已升级到期望的配置。 

    **说明：** 本例仅介绍了按量付费类型的Master节点的升配方式，关于更多类型升配方式，请参见[升降配方式汇总](../../../../intl.zh-CN/实例/升降配实例/升降配方式汇总.md#)。


