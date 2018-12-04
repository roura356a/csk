# Release Notes {#concept_qrb_znb_yfb .concept}

本文为您介绍了容器服务Kubernetes相关内容的最新动态。

|变更时间|变更分类|变更内容|产品文档|
|----|----|----|----|
|2018年10月|新地域| 容器服务Kubernetes 深圳金融云上线。

 容器服务Kubernetes可以在深圳金融云部署，享受金融云的安全合规保障。

 |N/A|
|新功能|容器服务Kubernetes 托管版国际站上线。|N/A|
|新功能|容器服务Kubernetes 应用管理优化，支持Deployment版本管理和回滚。|N/A|
|新功能|支持Istio与容器服务Kubernetes集群深度集成。|详见Istio管理的[概述](../../../../cn.zh-CN/用户指南/Kubernetes 集群/Istio管理/概述.md#)|
|2018年9月|新功能|支持容器服务Kubernetes 支持1.11版本。-   包括CRD增强、CoreDNS GA、Pod优先级和抢占式调度等特性。
-   支持多版本，可以按需选择1.10或1.11。
-   控制台支持多容器和有状态应用。

|[使用镜像创建有状态StatefulSet应用](../../../../cn.zh-CN/用户指南/Kubernetes 集群/应用管理/使用镜像创建有状态StatefulSet应用.md#)|
|新功能|容器服务Kubernetes支持免密拉取阿里云镜像服务私有仓库。| [阿里云Kubernetes容器服务支持免密拉取私有镜像仓库](https://yq.aliyun.com/articles/640906)

 |
|新功能|支持节点自动伸缩。|[自动伸缩集群](../../../../cn.zh-CN/用户指南/Kubernetes 集群/集群管理/自动伸缩集群.md#)|
|新功能|支持竞价实例。| [阿里云容器服务kubernetes发布竞价实例支持](https://yq.aliyun.com/articles/640929)

 |
|2018年8月|新功能|容器服务Kubernetes 托管版公测上线。|[创建Kubernetes 托管版集群](../../../../cn.zh-CN/用户指南/Kubernetes 集群/集群管理/创建Kubernetes 托管版集群.md#)|
|新功能|Istio Addon发布。|N/A|
|2018年7月|新地域|容器服务Kubernetes 澳洲上线。|N/A|
|新功能|支持灰度和分批发布。|详见Kubernetes集群中通过 Ingress 实现灰度发布和蓝绿发布的[概述](../../../../cn.zh-CN/最佳实践/发布/Kubernetes集群中通过 Ingress 实现灰度发布和蓝绿发布/概述.md#)以及[在阿里云容器服务Kubernetes上使用分批发布](../../../../cn.zh-CN/用户指南/Kubernetes 集群/发布管理/在阿里云容器服务Kubernetes上使用分批发布.md#)|
|2018年6月|新地域|容器服务Kubernetes 日本站、华北5上线。|N/A|
|新功能|容器服务Kubernetes 1.10版本支持FPGA、HugePages等特性。|N/A|
|新功能|容器服务Kubernetes监控增强，支持应用监控和报警。|[与云监控集成与使用](../../../../cn.zh-CN/用户指南/Kubernetes 集群/监控管理/与云监控集成与使用.md#)|
|新功能|容器服务Kubernetes支持直接创建包年包月集群。|[创建Kubernetes集群](../../../../cn.zh-CN/用户指南/Kubernetes 集群/集群管理/创建Kubernetes集群.md#)|
|新功能|容器服务Serverless Kubernetes 发布对exec/attach和ingress的支持。|[Kubernetes 功能支持](../../../../cn.zh-CN/用户指南/Serverless Kubernetes 集群/Kubernetes 功能支持.md#)|
|2018年5月|新地域| 容器服务Kubernetes 上海金融云上线。

 容器服务Kubernetes可以在上海金融云部署，享受金融云的安全合规保障。

 |N/A|
|新功能|容器服务 Serverless Kubernetes 发布。|[Serverless Kubernetes 集群](cn.zh-CN/产品简介/Serverless Kubernetes 集群.md#)|
|新功能|容器服务Kubernetes 支持蓝绿发布、灰度发布和AB测试|详见Kubernetes集群中通过 Ingress 实现灰度发布和蓝绿发布的[概述](../../../../cn.zh-CN/最佳实践/发布/Kubernetes集群中通过 Ingress 实现灰度发布和蓝绿发布/概述.md#)|
|2018年4月|新地域| 容器服务Kubernetes 印度、印尼、迪拜地域上线。

 容器服务Kubernetes已经在东南亚、中东和印度的5个地域全部上线。皆可使用最新稳定的1.9版本。

 |N/A|
|新功能| 容器服务多可用区Kubernetes高可用集群发布。

 支持将Master部署到3个可用区，而woker节点也可以根据需要选择部署在多个可用区。从而最大程度实现集群的生产可用。

 |[创建多可用区 Kubernetes 集群](../../../../cn.zh-CN/用户指南/Kubernetes 集群/集群管理/创建多可用区 Kubernetes 集群.md#)|
|新功能|容器服务Kubernetes 服务目录更新，支持MySQL、RDS、RabbitMQ和Spark。|详见服务目录管理的[概述](../../../../cn.zh-CN/用户指南/Kubernetes 集群/服务目录管理/概述.md#)|
|新功能|容器服务Kubernetes 应用目录更新，支持Helm Release管理。|[基于Helm的发布管理](../../../../cn.zh-CN/用户指南/Kubernetes 集群/发布管理/基于Helm的发布管理.md#)|
|2018年3月|新功能| 容器服务Kubernetes 支持1.9版本，支持自定义ECS镜像。

 容器服务支持原生Kubernetes 1.9.3版本，Workloads API正式发布，CRD默认开启，支持GPU调度。除此之外，在创建集群时，可选择自定义ECS镜像。在添加节点时，支持自动化重置镜像的方式加入

 |N/A|
|新功能|容器服务Kubernetes 应用目录发布，支持通过Helm一键部署应用。|[基于Helm的发布管理](../../../../cn.zh-CN/用户指南/Kubernetes 集群/发布管理/基于Helm的发布管理.md#)|
|新功能|容器服务Kubernetes 服务目录发布，支持ServiceBroker。|详见服务目录管理的[概述](../../../../cn.zh-CN/用户指南/Kubernetes 集群/服务目录管理/概述.md#)|
|新功能|容器服务Kubernetes 资源监控增强，支持通过云监控提供节点监控。|[通过资源分组进行监控与告警](../../../../cn.zh-CN/用户指南/Kubernetes 集群/监控管理/通过资源分组进行监控与告警.md#)|
|2018年1月|新地域|容器服务Kubernetes 和容器镜像服务 国际站上线。|N/A|
|新功能|容器服务Kubernetes支持1.8.4版本，并提供额外安全增强、弹性伸缩等功能。|[自动伸缩集群](../../../../cn.zh-CN/用户指南/Kubernetes 集群/集群管理/自动伸缩集群.md#)|
|新功能|容器服务Kubernetes FlexVolume数据卷发布，支持云盘、NAS和OSS。| [使用阿里云云盘](../../../../cn.zh-CN/用户指南/Kubernetes 集群/存储管理/使用阿里云云盘.md#)

 [使用阿里云 NAS](../../../../cn.zh-CN/用户指南/Kubernetes 集群/存储管理/使用阿里云 NAS.md#)

 [使用阿里云 OSS](../../../../cn.zh-CN/用户指南/Kubernetes 集群/存储管理/使用阿里云 OSS.md#)

 |
|新功能|容器服务Kubernetes 网络功能增强，支持Network Policy和带宽限制。|[通过负载均衡（Server Load Balancer）访问服务](../../../../cn.zh-CN/用户指南/Kubernetes 集群/负载均衡及路由管理/通过负载均衡（Server Load Balancer）访问服务.md#)|
|新功能|容器服务Kubernetes 支持弹性裸金属服务器。|N/A|
|2017年10月|新功能| 原生Kubernetes新版本上线。

 容器服务Kubernetes 支持1.8.1版本。

 |[什么是容器服务 Kubernetes版](cn.zh-CN/产品简介/什么是容器服务 Kubernetes版.md#)|
|新功能|区块链解决方案公测。|详见区块链解决方案的[概述](../../../../cn.zh-CN/解决方案/区块链解决方案/概述.md#)|
|2017年8月|新功能|容器服务Kubernetes 支持1.7.2版本。|N/A|

