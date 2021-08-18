---
keyword: [Windows节点池, 创建Windows节点池]
---

# 创建Windows节点池

您可以通过节点池管理集群中的一组节点资源，例如在节点池中统一管理节点的标签和污点。本文介绍如何基于容器服务Kubernetes版（ACK）集群中的节点池功能创建Windows节点。

-   您已创建一个ACK集群，请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   请确保您集群的Kubernetes版本大于1.9。

**说明：**

-   Windows操作系统的节点池仅支持Flannel网络，不支持Terway网络。

-   默认情况下，每个集群中最多可包含100个节点。如果您需要添加更多节点，请[提交工单](https://workorder-intl.console.aliyun.com/console.htm)申请。
-   添加已有云服务器时，请确保您的专有网络中的ECS实例绑定一个弹性公网IP（EIP），或者相应VPC已经配置了NAT网关。您需要确保相应节点能正常访问公网，否则，添加云服务器会失败。

-   Windows节点池支持的操作系统为Windows Server 2019和Windows Server Core，Version 1909/2004。

    **说明：** ACK控制台支持选择Windows Server 2019和Windows Server Core，Version 2004。如果您有使用Windows Server Core，Version 1909的需求，请根据ECS公共镜像发布记录来选择最新的镜像ID创建节点池。更多信息，请参见[公共镜像发布记录](/intl.zh-CN/镜像/公共镜像/公共镜像发布记录.md)。

-   Windows节点池的机器规格请选择4核8 GB以上的规格。
    -   Windows容器在内存使用超出Limitation后并不会执行OOM Killed。从v1.20.4-aliyun.1版本开始，ACK的Windows节点启动时会预留一定的资源（1.5核CPU、2.5GB RAM、3GB DISK），以保证Windows系统、kubelet、容器运行时的稳定运行。

        **说明：** ACK通过预留资源机制，可以减少或避免因Windows工作负载的过度分配而导致Windows节点完全不可用的场景产生。但当Windows容器应用发生内存泄露时，依然存在击垮Windows节点的可能性。

    -   Windows容器有一定的Footprint。更多信息，请参见[Windows容器的内存要求](https://docs.microsoft.com/en-us/virtualization/windowscontainers/deploy-containers/system-requirements#memory-requirements)。

## 操作步骤

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**节点管理** \> **节点池**。

5.  在节点池页面右上角，单击**创建节点池**。

    您还可以在节点池页面右上角，单击**创建托管节点池**或者**自动弹性伸缩配置**创建托管节点池或者具有弹性功能的节点池。

6.  在创建节点池页面，设置创建节点池的配置项。

    有关配置项的详细说明，请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。部分配置项说明如下：

    -   数量：设置节点池初始节点数量。如不需要创建节点，可以填写为0。
    -   操作系统：选择Windows操作系统。
    -   节点标签：为集群节点添加标签。
    -   ECS标签：为ECS实例添加标签。
7.  单击**确认配置**。

    在节点池页面，如果节点池**状态**显示**初始化中**，则说明节点池正在创建中。创建完成后，**状态**显示为**已激活**。

    ![nodepool](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/9106659951/p95881.png)


