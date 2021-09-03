---
keyword: [Kubernetes集群, 功能, K8s集群, 概述]
---

# ACK集群概述

Kubernetes是主流的开源容器编排平台，用于管理容器化应用和服务。为了让您可以方便地在阿里云上使用Kubernetes管理容器应用，阿里云容器服务ACK基于原生的Kubernetes，整合阿里云虚拟化、存储、网络和安全等能力，提供安全、高性能、可伸缩的ACK集群，简化Kubernetes集群的搭建和扩容等工作，让您专注于容器化的应用的开发与管理。本文介绍ACK集群的功能、类型和使用限制等。

## 背景信息

容器服务ACK面向多种业务场景提供多样化的Kubernetes集群：

-   ACK集群：适合大多数业务场景，是一种最通用的Kubernetes集群。
-   ASK集群：适合快速伸缩的敏捷业务场景以及单个或多个任务处理的场景。更多信息，请参见[ASK概述](/cn.zh-CN/Serverless Kubernetes集群用户指南/ASK概述.md)。
-   边缘集群：是IoT、CDN等边缘业务的必选。更多信息，请参见[ACK@Edge概述](/cn.zh-CN/边缘容器服务ACK@Edge用户指南/ACK@Edge概述.md)。

此外，容器服务ACK在基因计算、AI大数据等领域提供了高度集成的解决方案，结合IaaS高性能计算、网络能力，发挥容器的最佳性能。在多云混合云领域，容器服务ACK提供了多集群统一管理能力，您可在容器服务控制台，统一管理来自线下IDC，或者其他云上的Kubernetes集群。

## 使用限制

关于ACK集群的使用限制，请参见[使用限制](/cn.zh-CN/产品简介/使用限制.md)。

## 集群生命周期

ACK集群在不同状态下的含义和集群的状态流转图如下。

|状态|说明|
|--|--|
|初始化中（initial）|正在申请集群相应的云资源。|
|创建失败（failed）|申请集群云资源失败。|
|运行中（running）|成功申请集群云资源。|
|更新中（updating）|更新集群元信息。|
|扩容中（scaling）|扩容或添加集群节点。|
|移除节点中（removing）|移除集群节点中。|
|升级中（upgrading）|升级集群中。|
|删除中（deleting）|删除集群中。|
|删除失败（delete\_failed）|删除集群失败。|
|已删除（deleted，该状态用户不可见）|成功删除集群。|

![集体状态轮转](../images/p4752.png "集群状态流转")

## 集群类型

ACK集群包括Pro版本、标准版、专有版三种类型。

|比较项|ACK Pro版|ACK标准版|ACK专有版|
|---|--------|------|------|
|主要特点|您只需创建Worker节点，Master节点由ACK创建并托管。|您需要自行创建Master节点及Worker节点。|
|简单、低成本、高可用，无需管理Master节点。关于ACK集群标准版和Pro版本的具体区别，请参见[对比](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/ACK Pro版集群介绍.md)。

|简单、低成本、高可用，无需管理Master节点。|可以对集群基础设施进行更细粒度的控制，需要自行规划、维护、升级服务器集群。|
|收费方式|收取集群管理费用，按照集群数量方式收费，同时但需要承担Worker节点以及其他基础资源的费用。更多信息，请参见[产品计费](/cn.zh-CN/.md)。|集群管理免费，但需要承担Worker节点以及其他基础资源的费用。更多信息，请参见[产品计费](/cn.zh-CN/.md)。|集群管理免费，但需要承担Master节点、Worker节点以及其他基础资源的费用。更多信息，请参见[产品计费](/cn.zh-CN/.md)。|
|应用场景|适用于企业生产与测试环境。|适用于个人学习与测试。|适用于K8s研究与定制化场景。|
|用户画像|![2](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/5748490061/p143466.png)

|![专有版](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/5748490061/p143465.png) |
|集群创建|![托管版](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/4868490061/p143472.png)

|![专有版](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/4868490061/p143471.png) |

## 产品功能

ACK集群支持的功能如下表。

|功能|描述|
|--|--|
|集群管理|-   集群创建：您可根据需求创建多种形态集群，选择类型丰富的工作节点，并进行灵活的自定义配置。
-   集群升级：一键升级K8s版本，统一管理系统组件升级。
-   节点池管理：支持节点池生命周期管理，支持在同一集群中配置不同规格的节点池，例如交换机、运行时、OS、安全组等。
-   [弹性伸缩](/cn.zh-CN/Kubernetes集群用户指南/弹性伸缩/弹性伸缩概述.md)：通过控制台一键垂直扩缩容来快速应对业务波动，同时支持服务级别的亲和性策略和横向扩展。
-   [多集群管理](/cn.zh-CN/Kubernetes集群用户指南/多云混合云/多云混合云概述.md)：支持线下IDC和多云多区域的集群统一接入实现混合云应用管理。
-   授权管理：支持RAM授权和RBAC权限管理。 |
|节点池|支持节点池生命周期管理，支持在同一集群中配置不同规格的节点池，例如交换机、运行时、OS、安全组等。更多信息，请参见[节点池概述](/cn.zh-CN/Kubernetes集群用户指南/节点与节点池/节点池/节点池概述.md)。 |
|应用管理|-   应用创建：支持多种类型应用，从镜像、模版的创建，支持环境变量、应用健康、数据盘、日志等相关配置。
-   应用全生命周期：支持应用查看、更新、删除，应用历史版本回滚、应用事件查看、应用滚动升级、应用替换升级以及通过触发器重新部署应用。
-   应用调度：支持节点间亲和性调度、应用间亲和性调度、应用间反亲和性调度三种策略。
-   应用伸缩：支持手动伸缩应用容器实例，HPA自动伸缩策略。
-   应用发布：支持灰度发布和蓝绿发布。
-   应用目录：支持应用目录，简化云服务集成。
-   [应用中心](/cn.zh-CN/Kubernetes集群用户指南/应用中心（旧版）/应用中心概述.md)：应用部署后，以统一的视角展现整体应用的拓扑结构，同时对于持续部署等场景进行统一的版本管理与回滚。
-   应用备份和恢复：支持对Kubernetes应用进行备份和恢复。更多信息，请参见[备份和恢复应用](/cn.zh-CN/Kubernetes集群用户指南/备份中心/备份和恢复应用.md)。 |
|存储|-   [存储插件](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/存储CSI概述.md)：支持Flexvolume以及CSI。
-   存储卷和存储声明：
    -   支持创建块存储、NAS、OSS、CPFS类型的存储卷。
    -   支持持久化存储卷声明（PVC）挂接存储卷。
    -   支持存储卷的动态创建和迁移。
    -   支持以脚本方式查看和更新存储卷和存储声明。
-   [网络路由](/cn.zh-CN/Kubernetes集群用户指南/网络/网络概述.md)：
    -   支持VPC Flannel模式、Terway容器网络。
    -   支持定义Sevice和Pod的CIDR。
    -   支持NetworkPolicy。
    -   支持路由Ingress。 |
|网络| |
|运维与安全|-   监控：支持集群、节点、应用、容器实例层面的监控；支持prometheus插件。
-   日志：支持集群日志查看；支持应用日志采集；支持容器实例日志查看。
-   [安全中心](/cn.zh-CN/Kubernetes集群用户指南/安全/容器应用安全/使用运行时刻安全监控.md)：支持运行时刻的安全策略管理，应用安全配置巡检和运行时刻的安全监控和告警，提升容器安全整体纵深防御能力。
-   [安全沙箱](/cn.zh-CN/Kubernetes集群用户指南/安全沙箱/安全沙箱概述.md)：可以让应用运行在一个轻量虚拟机沙箱环境中，拥有独立的内核，具备更好的安全隔离能力。适用于不可信应用隔离、故障隔离、性能隔离、多用户间负载隔离等场景。
-   [机密计算](/cn.zh-CN/Kubernetes集群用户指南/ACK-TEE机密计算/ACK-TEE机密计算介绍.md)：基于Intel SGX提供的可信应用或用于交付和管理机密计算应用的云原生一站式机密计算平台，帮助您保护数据使用中的安全性、完整性和机密性。机密计算可以让您把重要的数据和代码放在一个特殊的可信执行加密环境。 |
|异构计算|-   GPU和NPU：支持创建以GPU或NPU型实例作为工作节点的集群，并支持GPU调度、GPU监控、GPU弹性伸缩、GPU运维管理等。更多信息，请参见[创建托管GPU集群](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/创建异构计算集群/创建托管GPU集群.md)。
-   共享GPU：支持在云平台和自己的数据中心的集群中通过GPU共享调度框架实现多个容器运行在同一个GPU设备。更多信息，请参见[共享GPU调度概述](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/共享GPU调度/共享GPU调度概述.md)。
-   云原生AI：提供了云原生AI能力，支持编排、管理数据计算类任务。更多信息，请参见[云原生AI概述](/cn.zh-CN/云原生AI用户指南/云原生AI概述.md)。 |
|开发者服务|-   API：提供OpenAPI和社区原生API。更多信息，请参见[使用Kubernetes API](/cn.zh-CN/API参考/使用Kubernetes API.md)和[API概览](/cn.zh-CN/API参考/API概览.md)。
-   Cloudshell：提供阿里云Cloudshell和社区原生Kubectl工具。更多信息，请参见[在CloudShell上通过kubectl管理Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/在CloudShell上通过kubectl管理Kubernetes集群.md)和[通过kubectl工具连接集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl工具连接集群.md)。 |

## 开源项目

有关容器服务ACK使用的开源项目，请参见[t2010588.dita\#concept\_2010588](/cn.zh-CN/产品简介/开源项目.md)。

## 常见问题

|-   [授权管理FAQ](/cn.zh-CN/Kubernetes集群用户指南/授权/授权管理FAQ.md)
-   [集群管理FAQ](/cn.zh-CN/Kubernetes集群用户指南/集群/集群管理FAQ.md)
-   [节点管理FAQ](/cn.zh-CN/Kubernetes集群用户指南/节点与节点池/节点管理FAQ.md)
-   [容器网络FAQ](/cn.zh-CN/Kubernetes集群用户指南/网络/容器网络CNI/容器网络FAQ.md)
-   [Service FAQ](/cn.zh-CN/Kubernetes集群用户指南/网络/Service管理/Service FAQ.md)
-   [Ingress FAQ](/cn.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/Ingress FAQ.md)
-   [多云混合云FAQ](/cn.zh-CN/Kubernetes集群用户指南/多云混合云/多云混合云FAQ.md)

|-   [应用FAQ](/cn.zh-CN/Kubernetes集群用户指南/应用/应用FAQ.md)
-   [容器安全FAQ](/cn.zh-CN/Kubernetes集群用户指南/安全/容器安全FAQ.md)
-   [存储FAQ-CSI](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/存储FAQ-CSI.md)
-   [存储FAQ-Flexvolume](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/存储FAQ-Flexvolume.md)
-   [GPU/NPU FAQ](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU/NPU FAQ.md)
-   [弹性伸缩FAQ](/cn.zh-CN/Kubernetes集群用户指南/弹性伸缩/弹性伸缩FAQ.md) |

