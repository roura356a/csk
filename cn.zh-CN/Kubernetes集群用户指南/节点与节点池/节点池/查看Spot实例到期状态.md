---
keyword: [实例到期检测, Spot实例]
---

# 查看Spot实例到期状态

为了防止Spot实例（即抢占式实例）到期引发节点意外退出，阿里云容器服务ACK可通过ack-node-problem-detector组件获取实例即将释放的信息并通知给您。本文主要介绍如何查看Spot实例到期状态。

创建Kubernetes集群且安装最新版本的ack-node-problem-detector组件。具体操作，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。

-   若需要新建集群，请选中**安装node-problem-detector并创建事件中心**。
-   若使用已有集群，请确认ack-node-problem-detector组件为最新版本。具体操作，请参见[管理组件](/cn.zh-CN/Kubernetes集群用户指南/组件/管理组件.md)。

在阿里云容器服务ACK中，ECS实例作为节点支持着集群以及集群上运行的服务。根据ECS实例创建时的策略，某些实例（例如Spot实例、包年包月实例）存在到期自动释放问题。若在实例自动释放时未做Pod驱逐、节点排水、节点替换等前置处理，可能影响集群服务运行情况，Master节点意外退出可能引发集群级别的故障。为了防止Spot实例到期引发的节点意外退出问题，可通过ack-node-problem-detector组件的InstanceExpired状态获取实例即将释放的信息。

## 操作步骤

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**节点管理** \> **节点**。

5.  在节点页面，单击目标节点的名称或在目标节点右侧**操作**列选择**更多** \> **详情**。

6.  在节点详情页面查看InstanceExpired类型的状态。

    在**状态**区域查看InstanceExpired类型的状态。

    ![实例是否到期](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/3771858261/p302105.png)

    InstanceExpired的状态说明如下所示：

    |InstanceExpired的状态|说明|
    |------------------|--|
    |True|若**InstanceExpired**的状态为True，**内容**为**InstanceToBeTerminated**，表示Spot实例将到期，会被释放。|
    |False|若**InstanceExpired**的状态为False，**内容**为**InstanceNotToBeTerminated**，表示Spot实例还未到期，可持续使用。|
    |Unknown|表示插件运行错误，请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)解决。|

    若InstanceExpired的状态为**True**，可在**事件**区域看到以下的事件。

    ![实例过期event](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/3771858261/p302240.png)


若InstanceExpired的状态为True，表示Spot实例将到期释放。若您需要继续使用该节点上的服务，请将应用调度到其他节点上。具体操作，请参见[调度应用Pod至指定节点](/cn.zh-CN/Kubernetes集群用户指南/应用/应用调度部署/调度应用Pod至指定节点.md)。

