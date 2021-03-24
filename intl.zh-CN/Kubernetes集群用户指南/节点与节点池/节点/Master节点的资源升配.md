---
keyword: [Master节点, 修改节点规格, 节点升配]
---

# Master节点的资源升配

Kubernetes专有版集群的Master节点由用户自己管控，随着集群规模的不断扩大，用户可能会遇到Master节点的配置不足的情况，本文为您介绍如何升级Master的配置。

[创建Kubernetes专有版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes专有版集群.md)

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  找到集群的三个Master节点，单击Master节点的实例ID，进入实例详情页面（本文以master-01为例）。

    此时，您可以看到实例规格等信息。

    ![实例详情](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5463659951/p50527.png)

5.  在实例的配置信息区域，单击**更改实例规格**。

    ![更改实例规格](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5463659951/p50517.png)

    如果**更改实例规格**为灰显，说明实例正在运行 ，您需要单击界面右上角的**停止**，停止运行该实例，再进行下一步。

6.  在弹出的调整规格页面，选择合适的规格，单击**确认调整**。节点规格请参见[选择Master节点规格](/intl.zh-CN/最佳实践/集群/ECS选型.md)。

7.  在弹出的调整规格成功页面，单击**确认**。

    此时自动跳转到实例列表页面。

    ![实例列表](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5463659951/p50514.png)

    可以看到master-01节点的规格已经调整成功。

8.  勾选目标实例，单击**启动**，等待升配的master-01节点自动加入集群且状态变成**运行中**，如图所示。

    ![节点列表](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5463659951/p50530.png)

9.  重复步骤4-7，直至所有Master节点均已升级到期望的配置。

    **说明：** 本例仅介绍了按量付费类型的Master节点的升配方式，关于更多类型升配方式，请参见[升降配方式概述](/intl.zh-CN/实例/升降配实例/升降配方式概述.md)。


