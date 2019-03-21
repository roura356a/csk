# Release Notes {#concept_qrb_znb_yfb .concept}

本文为您介绍了容器服务Kubernetes相关内容的最新动态。

|变更时间|变更分类|变更内容|产品文档|
|----|----|----|----|
|2019年2月|新地域|Kubernetes托管版深圳上线。用户可以在中国站和国际站的深圳区域使用Kubernetes托管版。

使用Kubernetes托管版的核心优势有：

-   节省资源，每个集群节省3个master节点。
-   运维简单，ACK负责帮助托管master集群。
-   安全，ACK护航满足用户安全需求。

|N/A|
|新功能| Knative是一种可缩放至零、请求驱动的计算运行环境，构建在 Kubernetes 和 Istio 之上，支持为 serverless 应用、函数提供部署与服务。

 ACK推出Knative Addon插件，帮助用户能够基于ACK集群快速搭建Knative Serving环境。

 |N/A

|
|新功能|智能运维集群检查功能在中国站全区域开放。集群检查可以深度检查集群资源、组件、配置等，帮助用户快速定位集群使用问题。

|N/A|
|2019年1月|新功能|Windows容器内测上线。ACK支持Windows容器，Windows下的应用也可以容器化运行在Kubernetes上，享受Kubernetes弹性调度管理所带来的优势。

用户可以在ACK的Kubernetes集群以及托管版Kubernetes集群中通过添加节点的方式来添加Windows节点。

目前该功能处于内测状态，想要体验的用户可通过提交工单申请。

 |N/A|
|新功能|容器镜像服务企业版内测上线。与目前的免费镜像仓库不同，企业版为用户部署一整套构建在独立资源上的容器镜像仓库，提供企业级的镜像安全托管功能、更强的大规模镜像分发能力，以及稳定的镜像构建服务，适合拥有大规模集群节点并且安全需求较高的企业级客户。

目前该功能处于内测状态，想要体验的用户可通过提交工单申请。

|N/A|
|新功能|集群智能运维上线。智能运维的目的是用户提供不同场景下的容器集群使用的最优解决方案，该功能在杭州地区已上线。

用户可以进行深度检查集群资源、组件、配置等信息，帮助用户快速定位集群使用问题。

|N/A|
|新功能|容器服务支持ARMS应用实时监控。容器服务已集成ARMS业务实时监控服务的能力，用户在集群中安装相应的arms插件后，即可对集群中所部署的应用进行性能的监控。

ARMS是一款针对 Java 应用的性能管理（APM）软件。无需修改任何代码，只需在 Java 应用的启动脚本中挂载一个探针，该探针就能够对用户的 Java 应用进行全方位监控，例如快速定位出错接口和慢接口、重现调用参数、检测内存泄漏、发现系统瓶颈等，从而大幅提升线上问题诊断问题的效率。

|[接入容器服务 Kubernetes 版应用](https://www.alibabacloud.com/help/zh/doc-detail/103106.html)|
|商用|Serverless Kubernetes底层所调度的容器实例ECI开始商用化收费。2019年01月22日起，Serverless Kubernetes的底层所调度的ECI容器实例开始商用收费，用户在使用Serverless Kubernetes集群创建容器实例时会有计费产生。Serverless Kubernetes本身继续为用户提供免费服务。

|Serverless Kubernetes底层所调度的容器实例ECI收费价格标准参考[计费说明](https://www.alibabacloud.com/help/zh/doc-detail/89142.html)|
|新地域|Serverless Kubernetes在中国站的北京、深圳上线。Serverless Kubernetes可以在北京、深圳区域可以部署，享受无服务器容器带来的极致体验。

|N/A|
|2018年12月|新地域|容器服务英国（伦敦）上线。用户可以在中国站和国际站的伦敦区域使用ACK，享受Kubernetes容器带来的云原生能力。

|N/A|
|新地域|Kubernetes托管版上海、马来西亚、印度上线|N/A|
|新功能|支持在集群中移除节点。用户可以从集群中移除指定节点，移除同时并且可选择释放ECS与否。

|[移除节点](../../../../../intl.zh-CN/用户指南/Kubernetes集群/节点管理/移除节点.md#)|
|新功能|支持开放守护进程集（DaemonSet）应用的部署。用户可以创建DaemonSet类型的应用，DaemonSet是一种守护进程，可以在集群的每一个节点上有且只有一个Pod。

|N/A|
|新功能|支持自定义Istio网关。用户可以自定义Istio的入口和出口网关，支持通过不同参数定制化。

|N/A

|
|新功能|支持Istio CoreDNS。实现基于CoreDNS Plugin扩展实现Istio Service Entry的DNS寻址。

|N/A

|
|新功能|托管版Kubernetes集群功能增强，创建集群时支持使用已有ECS。用户可以在创建托管版Kubernetes集群时直接选择已有的ECS节点，而不必新建节点。

|[创建Kubernetes 托管版集群](../../../../../intl.zh-CN/用户指南/Kubernetes集群/集群管理/创建Kubernetes 托管版集群.md#)|
|2018年11月|新地域|Kubernetes托管版国际站印尼区上线。用户可以在国际站的印尼区域使用ACK，享受Kubernetes容器带来的云原生能力。

|N/A|
|商用|网络插件Terway上线。阿里云容器服务推出高性能网络插件Terway，支持容器直接通过ENI通信，性能比VPC Flannel更高。

|[Terway网络插件](../../../../../intl.zh-CN/用户指南/Kubernetes集群/网络管理/Terway网络插件.md#)|
|新功能|支持Worker节点性能指标缩略图显示，更加方便用户查看节点的状况。|N/A|
|新功能|集群添加已有节点时支持批量添加，还可以选择多台节点批量加入Kubernetes集群。|N/A|
|新功能|集群支持证书滚动，防止证书过期。|N/A|
|2018年10月|新地域| 容器服务Kubernetes 深圳金融云上线。

 容器服务Kubernetes可以在深圳金融云部署，享受金融云的安全合规保障。

 |N/A|
|新功能|容器服务Kubernetes 托管版国际站上线。|N/A|
|新功能|容器服务Kubernetes 应用管理优化，支持Deployment版本管理和回滚。|N/A|
|新功能|支持Istio与容器服务Kubernetes集群深度集成。|详见Istio管理的[概述](../../../../../intl.zh-CN/用户指南/Kubernetes集群/Istio管理/概述.md#)|
|2018年9月|新功能|支持容器服务Kubernetes 支持1.11版本。-   包括CRD增强、CoreDNS GA、Pod优先级和抢占式调度等特性。
-   支持多版本，可以按需选择1.10或1.11。
-   控制台支持多容器和有状态应用。

|[使用镜像创建有状态StatefulSet应用](../../../../../intl.zh-CN/用户指南/Kubernetes集群/应用管理/使用镜像创建有状态StatefulSet应用.md#)|
|新功能|容器服务Kubernetes支持免密拉取阿里云镜像服务私有仓库。| N/A

 |
|新功能|支持节点自动伸缩。|[自动伸缩集群](../../../../../intl.zh-CN/用户指南/Kubernetes集群/集群管理/自动伸缩集群.md#)|
|新功能|支持竞价实例。| N/A

 |
|2018年8月|新功能|容器服务Kubernetes 托管版公测上线。|[创建Kubernetes 托管版集群](../../../../../intl.zh-CN/用户指南/Kubernetes集群/集群管理/创建Kubernetes 托管版集群.md#)|
|新功能|Istio Addon发布。|N/A|
|2018年7月|新地域|容器服务Kubernetes 澳洲上线。|N/A|
|新功能|支持灰度和分批发布。|详见Kubernetes集群中通过 Ingress 实现灰度发布和蓝绿发布的[概述](../../../../../intl.zh-CN/最佳实践/发布/Kubernetes集群中通过 Ingress 实现灰度发布和蓝绿发布/概述.md#)以及[在阿里云容器服务Kubernetes上使用分批发布](../../../../../intl.zh-CN/用户指南/Kubernetes集群/发布管理/在阿里云容器服务Kubernetes上使用分批发布.md#)|
|2018年6月|新地域|容器服务Kubernetes 日本站、华北5上线。|N/A|
|新功能|容器服务Kubernetes 1.10版本支持FPGA、HugePages等特性。|N/A|
|新功能|容器服务Kubernetes监控增强，支持应用监控和报警。|[与云监控集成与使用](../../../../../intl.zh-CN/用户指南/Kubernetes集群/监控管理/与云监控集成与使用.md#)|
|新功能|容器服务Kubernetes支持直接创建包年包月集群。|[创建Kubernetes集群](../../../../../intl.zh-CN/用户指南/Kubernetes集群/集群管理/创建Kubernetes集群.md#)|
|新功能|容器服务Serverless Kubernetes 发布对exec/attach和ingress的支持。|[Kubernetes 功能支持](../../../../../intl.zh-CN/用户指南/Serverless Kubernetes 集群/Kubernetes 功能支持.md#)|
|2018年5月|新地域| 容器服务Kubernetes 上海金融云上线。

 容器服务Kubernetes可以在上海金融云部署，享受金融云的安全合规保障。

 |N/A|
|新功能|容器服务 Serverless Kubernetes 发布。|[Serverless Kubernetes 集群](intl.zh-CN/产品简介/Serverless Kubernetes 集群.md#)|
|新功能|容器服务Kubernetes 支持蓝绿发布、灰度发布和AB测试|详见Kubernetes集群中通过 Ingress 实现灰度发布和蓝绿发布的[概述](../../../../../intl.zh-CN/最佳实践/发布/Kubernetes集群中通过 Ingress 实现灰度发布和蓝绿发布/概述.md#)|
|2018年4月|新地域| 容器服务Kubernetes 印度、印尼、迪拜地域上线。

 容器服务Kubernetes已经在东南亚、中东和印度的5个地域全部上线。皆可使用最新稳定的1.9版本。

 |N/A|
|新功能| 容器服务多可用区Kubernetes高可用集群发布。

 支持将Master部署到3个可用区，而woker节点也可以根据需要选择部署在多个可用区。从而最大程度实现集群的生产可用。

 |[创建多可用区 Kubernetes 集群](../../../../../intl.zh-CN/用户指南/Kubernetes集群/集群管理/创建多可用区 Kubernetes 集群.md#)|
|新功能|容器服务Kubernetes 服务目录更新，支持MySQL、RDS、RabbitMQ和Spark。|详见服务目录管理的[概述](../../../../../intl.zh-CN/用户指南/Kubernetes集群/服务目录管理/概述.md#)|
|新功能|容器服务Kubernetes 应用目录更新，支持Helm Release管理。|[基于Helm的发布管理](../../../../../intl.zh-CN/用户指南/Kubernetes集群/发布管理/基于Helm的发布管理.md#)|
|2018年3月|新功能| 容器服务Kubernetes 支持1.9版本，支持自定义ECS镜像。

 容器服务支持原生Kubernetes 1.9.3版本，Workloads API正式发布，CRD默认开启，支持GPU调度。除此之外，在创建集群时，可选择自定义ECS镜像。在添加节点时，支持自动化重置镜像的方式加入。

 |N/A|
|新功能|容器服务Kubernetes 应用目录发布，支持通过Helm一键部署应用。|[基于Helm的发布管理](../../../../../intl.zh-CN/用户指南/Kubernetes集群/发布管理/基于Helm的发布管理.md#)|
|新功能|容器服务Kubernetes 服务目录发布，支持ServiceBroker。|详见服务目录管理的[概述](../../../../../intl.zh-CN/用户指南/Kubernetes集群/服务目录管理/概述.md#)|
|新功能|容器服务Kubernetes 资源监控增强，支持通过云监控提供节点监控。|[通过资源分组进行监控与告警](../../../../../intl.zh-CN/用户指南/Kubernetes集群/监控管理/通过资源分组进行监控与告警.md#)|
|2018年1月|新地域|容器服务Kubernetes 和容器镜像服务 国际站上线。|N/A|
|新功能|容器服务Kubernetes支持1.8.4版本，并提供额外安全增强、弹性伸缩等功能。|[自动伸缩集群](../../../../../intl.zh-CN/用户指南/Kubernetes集群/集群管理/自动伸缩集群.md#)|
|新功能|容器服务Kubernetes FlexVolume数据卷发布，支持云盘、NAS和OSS。| [使用阿里云云盘](../../../../../intl.zh-CN/用户指南/Kubernetes集群/存储管理/使用阿里云云盘.md#)

 [使用阿里云 NAS](../../../../../intl.zh-CN/用户指南/Kubernetes集群/存储管理/使用阿里云 NAS.md#)

 [使用阿里云 OSS](../../../../../intl.zh-CN/用户指南/Kubernetes集群/存储管理/使用阿里云 OSS.md#)

 |
|新功能|容器服务Kubernetes 网络功能增强，支持Network Policy和带宽限制。|[通过负载均衡（Server Load Balancer）访问服务](../../../../../intl.zh-CN/用户指南/Kubernetes集群/负载均衡及路由管理/通过负载均衡（Server Load Balancer）访问服务.md#)|
|新功能|容器服务Kubernetes 支持弹性裸金属服务器。|N/A|
|2017年10月|新功能| 原生Kubernetes新版本上线。

 容器服务Kubernetes 支持1.8.1版本。

 |[什么是容器服务 Kubernetes版](intl.zh-CN/产品简介/什么是容器服务 Kubernetes版.md#)|
|新功能|区块链解决方案公测。|详见区块链解决方案的[概述](../../../../../intl.zh-CN/解决方案/区块链解决方案/概述.md#)|
|2017年8月|新功能|容器服务Kubernetes 支持1.7.2版本。|N/A|

