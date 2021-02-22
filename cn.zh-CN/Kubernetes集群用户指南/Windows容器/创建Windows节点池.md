---
keyword: [Windows节点池, 创建Windows节点池]
---

# 创建Windows节点池

您可以通过节点池管理集群中的一组节点资源，例如在节点池中统一管理节点的标签和污点。本文介绍如何基于容器服务Kubernetes版（ACK）集群中的节点池功能创建Windows节点。

-   您已创建一个ACK集群，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes托管版集群.md)。
-   请确保您集群的Kubernetes版本大于1.9。

**说明：**

-   Windows操作系统的节点池仅支持Flannel网络，不支持Terway网络。

-   默认情况下，每个集群中最多可包含100个节点。如果您需要添加更多节点，请提交工单申请，请参见[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)。
-   添加已有云服务器时，请确保您的云服务器有EIP（专有网络），或者相应VPC已经配置了NAT网关。您需要确保相应节点能正常访问公网，否则，添加云服务器会失败。


## 操作步骤

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，单击**节点池**。

5.  在节点池页面右上角，单击**创建节点池**。

    您还可以在节点池页面右上角，单击**创建托管节点池**或者**自动弹性伸缩配置**创建托管版或者弹性节点池。

6.  在创建节点池页面，设置创建节点池的配置项。

    ![配置项](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9106659951/p95988.png)

    有关配置项的详细说明，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes托管版集群.md)。部分配置项说明如下：

    -   数量：设置节点池初始节点数量。如不需要创建节点，可以填写为0。
    -   操作系统：选择Windows操作系统。
    -   节点标签：为集群节点添加标签。
    -   ECS标签：为ECS实例添加标签。
7.  单击**确定配置**。

    在节点池页面，如果节点池**状态**显示**初始化中**，则说明节点池正在创建中。创建完成后，**状态**显示为**已激活**。

    ![nodepool](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9106659951/p95881.png)


