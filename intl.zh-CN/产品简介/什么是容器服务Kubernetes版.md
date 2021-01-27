---
keyword: [ACK, K8s, 容器服务, 容器服务Kubernetes版]
---

# 什么是容器服务Kubernetes版

阿里云容器服务Kubernetes版ACK（Alibaba Cloud Container Service for Kubernetes）是全球首批通过Kubernetes一致性认证的服务平台，提供高性能的容器应用管理服务，支持企业级Kubernetes容器化应用的生命周期管理，让您轻松高效地在云端运行Kubernetes容器化应用。

## 产品形态

ACK包含了专有版Kubernetes（Dedicated Kubernetes）、托管版Kubernetes（Managed Kubernetes）、Serverless Kubernetes三种形态，方便您按需选择。

|比较项|专有版Kubernetes|托管版Kubernetes|Serverless Kubernetes|
|---|-------------|-------------|---------------------|
|主要特点|您需要自行创建Master节点及Worker节点。|您只需创建Worker节点，Master节点由ACK创建并托管。|您无需创建Master节点及Worker节点。|
|可以对集群基础设施进行更细粒度的控制，需要自行规划、维护、升级服务器集群。|简单、低成本、高可用，无需管理Master节点。|无需管理任何节点，可直接启动应用程序。|
|收费方式|集群管理免费，但需要承担Master节点、Worker节点以及其他基础资源的费用。|-   标准版：集群管理免费，但需要承担Worker节点以及其他基础资源的费用。
-   [Pro版](/intl.zh-CN/Kubernetes集群用户指南/ACK Pro集群/Kubernetes Pro版集群介绍.md)：按照集群数量或者包年包月方式收费。

|按容器实例的使用资源量和时长（秒）计费。|
|应用场景|适用于所有场景。|适用于所有场景。|适用于批量任务、突发扩容，以及CI/CD测试。|
|用户画像|![专有版](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5748490061/p143465.png)

|![2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5748490061/p143466.png)

|![3](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5748490061/p143467.png) |
|集群创建|![专有版](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4868490061/p143471.png)

|![托管版](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4868490061/p143472.png)

|![ASK](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5748490061/p143473.png) |

## 产品功能

-   集群管理
    -   集群创建：您可根据需求创建多种形态集群，选择类型丰富的工作节点，并进行灵活的自定义配置。
    -   集群升级：一键升级K8s版本，统一管理系统组件升级。
    -   节点池管理：支持节点池生命周期管理，支持在同一集群中配置不同规格的节点池，例如交换机、运行时、OS、安全组等。
    -   [弹性伸缩](/intl.zh-CN/Kubernetes集群用户指南/弹性伸缩/弹性伸缩概述.md)：通过控制台一键垂直扩缩容来快速应对业务波动，同时支持服务级别的亲和性策略和横向扩展。
    -   [多集群管理](/intl.zh-CN/Kubernetes集群用户指南/多云混合云管理/多云混合云概述.md)：支持线下IDC和多云多区域的集群统一接入实现混合云应用管理。
    -   授权管理：支持RAM授权和RBAC权限管理。
-   应用管理
    -   应用创建：支持多种类型应用，从镜像、模版的创建，支持环境变量、应用健康、数据盘、日志等相关配置。
    -   应用全生命周期：支持应用查看、更新、删除，应用历史版本回滚、应用事件查看、应用滚动升级、应用替换升级以及通过触发器重新部署应用。
    -   应用调度：支持节点间亲和性调度、应用间亲和性调度、应用间反亲和性调度三种策略。
    -   应用伸缩：支持手动伸缩应用容器实例，HPA自动伸缩策略。
    -   应用发布：支持灰度发布和蓝绿发布。
    -   应用目录：支持应用目录，简化云服务集成。
    -   [应用中心]()：应用部署后，以统一的视角展现整体应用的拓扑结构，同时对于持续部署等场景进行统一的版本管理与回滚。
-   存储与网络
    -   [存储插件](/intl.zh-CN/Kubernetes集群用户指南/存储管理-Flexvolume/存储插件说明.md)：支持Flexvolume以及CSI。
    -   存储卷和存储声明：
        -   支持创建块存储、NAS、OSS、CPFS类型的存储卷。
        -   支持持久化存储卷声明（PVC）挂接存储卷。
        -   支持存储卷的动态创建和迁移。
        -   支持以脚本方式查看和更新存储卷和存储声明。
    -   [网络路由](/intl.zh-CN/Kubernetes集群用户指南/网络管理/网络概述.md)：
        -   支持VPC Flannel模式、Terway容器网络。
        -   支持定义Sevice和Pod的CIDR。
        -   支持NetworkPolicy。
        -   支持路由Ingress。
-   运维与安全
    -   监控：支持集群、节点、应用、容器实例层面的监控；支持prometheus插件。
    -   日志：支持集群日志查看；支持应用日志采集；支持容器实例日志查看。
    -   [安全中心](/intl.zh-CN/Kubernetes集群用户指南/安全管理/安全中心/使用运行时刻安全监控.md)：支持运行时刻的安全策略管理，应用安全配置巡检和运行时刻的安全监控和告警，提升容器安全整体纵深防御能力。
    -   [安全沙箱](/intl.zh-CN/Kubernetes集群用户指南/安全沙箱管理/安全沙箱概述.md)：可以让应用运行在一个轻量虚拟机沙箱环境中，拥有独立的内核，具备更好的安全隔离能力。适用于不可信应用隔离、故障隔离、性能隔离、多用户间负载隔离等场景。
    -   [机密计算](/intl.zh-CN/Kubernetes集群用户指南/ACK-TEE机密计算/ACK-TEE机密计算介绍.md)：基于Intel SGX提供的可信应用或用于交付和管理机密计算应用的云原生一站式机密计算平台，帮助您保护数据使用中的安全性、完整性和机密性。机密计算可以让您把重要的数据和代码放在一个特殊的可信执行加密环境。

## 产品架构

阿里云容器服务产品线的整体架构如下图所示。

![产品架构](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5748490061/p169914.png)

-   [阿里云容器镜像服务ACR]()（Alibaba Cloud Container Registry）：提供云原生资产的安全托管和全生命周期管理，支持多场景下镜像的高效分发，与容器服务ACK无缝集成，打造云原生应用一站式解决方案。
-   [阿里云服务网格ASM]()（Alibaba Cloud Service Mesh）：是一个托管式的微服务应用流量统一管理平台，兼容Istio，支持多个Kubernetes集群统一流量管理，为容器和虚拟机应用服务提供一致性的通信控制。
-   [阿里云Serverless Kubernetes ASK](/intl.zh-CN/Serverless Kubernetes集群用户指南/ASK概述.md)（Alibaba Cloud Serverless Kubernetes）：是阿里云基于弹性计算架构推出的无服务器Kubernetes容器服务，让您无需管理和维护集群，即可快速创建Kubernetes容器应用。
-   [阿里云基因计算AGS](/intl.zh-CN/基因计算服务AGS用户指南/AGS概览.md)（Alibaba Cloud Genomics Service）：是阿里云基于容器Kubernetes技术面向生物行业用户提供的基因大数据计算服务，具有高效、弹性、可靠的优点，相比传统的基因计算过程速度更快，成本更低。
-   [阿里云边缘容器服务ACK@Edge](/intl.zh-CN/边缘容器服务ACK@Edge用户指南/ACK@Edge概述.md)：基于标准Kubernetes运行环境，提供云、边、端一体的容器应用交付、运维和管控能力，同时加强在边缘业务场景下自治能力。

## 使用ACK

单击下方按钮可立即使用ACK。

