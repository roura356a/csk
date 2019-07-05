# Release Notes {#concept_qrb_znb_yfb .concept}

本文为您介绍了容器服务Kubernetes相关内容的最新动态。

## 2019年5月 {#section_ya0_qsu_myv .section}

-   **【新地域】Kubernetes 托管版开放公共云悉尼区域和金融云上海区域** 

    即日起，您可以在公共云的悉尼区域和金融云的上海区域使用Kubernetes托管版。详情请参见[什么是容器服务 ACK](ZH-CN_TP_15496_V5.md#)。

-   **【新功能】基因计算集群上线，专为基因计算打造** 

    容器服务在专有版Kubernetes推出基因计算集群，该集群具备高性能计算实例的工作节点，支持大型工作流引擎，支持大规模批量处理，适合BCL、FASTQ、BAM/SAM/VCF数据分析、装配、变异检查等计算业务。您可以通过控制台**集群** \> **集群**，创建集群时，在集群模板选择选择**基因计算专有集群**进行操作。

-   **【新功能】FPGA集群上线，支持视频图像加速** 

    容器服务推出FPGA集群，该集群以FPGA F3实例为工作节点，适用于视频编码H265、图片转码JPEG-to-HEIF等计算业务。FPGA视频加速由以前的一周以上到现在15分钟，大幅节省码率与优化同画质视频的CDN带宽成本。您可以通过控制台**集群** \> **集群**，创建集群时，在集群模板选择选择**FPGA专有集群**进行操作。

-   **【新功能】Cloud Controller Manger（CCM）升级新版本** 

    CCM组件升级到 v1.9.3.110-g4938309-aliyun，继续增强对SLB配置的支持。新支持的主要功能：

    -   支持通过配置参数限制创建公网SLB。
    -   支持修改证书ID。
    -   Service挂载内网SLB时可以指定Vswitch。
    -   支持SLB配置http 80端口转发到https 443端口。
-   **【新功能】Istio升级1.1.4，对接时间序列数据库TSDB** 

    新版本 Istio 1.1.4增强了Isito自愈能力，支持控制平面的自动恢复、旧版本的自动升级等。同时，容器服务ACK Istio对接了时间序列数据库TSDB。TSDB是一种集时序数据高效读写，压缩存储，实时计算能力为一体的数据库服务。针对Prometheus本地存储的痛点问题，TSDB为其提供了高性能、低成本、稳定可靠的在线远端存储服务。

    与社区提供的其他远端存储方案相比易用性高，只需修改Prometheus配置；集成程度高，免安装部署Adapter；同时支持读写，高度兼容PromQL；具备分布式弹性伸缩的存储能力。

-   **【新功能】容器镜像服务企业版支持镜像全球同步** 

    容器镜像服务（ACR）企业版镜像全球同步功能发布，支持不同地域间镜像自动同步，解决了用户在全球化容器应用交付中的痛点，帮助企业提高业务的创新迭代效率。容器镜像服务企业版，具备更强的安全防护及镜像规模化分发能力，适合安全需求高且拥有大规模节点的企业级客户。

-   **【功能强化】集群创建提供多可用区配置和5master超高可用集群** 

    即日起，用户可以在集群创建时为节点配置多个可用区，同时在专有版中可配置5个Master节点的超高可用集群，这样极大地提升了集群的可用性。


## 2019年4月 {#section_qx4_gkv_tsj .section}

-   **【新功能】Kubernetes 1.12.6版本升级功能开放** 

    Kubernetes 1.12.6 版本升级功能已在全区域开放，使用专有版和托管版 Kubernetes 集群的用户可以在控制台上将集群的版本从 1.11.5升级到 1.12.6。

-   **【新功能】托管版 Kubernetes 集群支持日志审计功能** 

    在Kubernetes托管版集群上也支持日志审计功能，审计日志针对APIServer记录相关日志，可以帮助集群管理人员记录或追溯不同用户的日常操作。详情请参见[Kube-apiserver审计日志](../../../../dita-oss-bucket/SP_235/DNcsk1877477/ZH-CN_TP_21467_V7.md#)。

-   **【新功能】Istio在v1.1版本实现通过控制台管理应用的功能** 

    容器服务升级Istio到v1.1版本，同时在控制台开放了应用管理功能。用户可以使用图形界面创建、管理Istio应用和服务，为应用创建灰度版本，设置灰度策略，以及配置故障注入策略。您可以通过控制台**服务网格** \> **虚拟服务**进行操作。

-   **【新功能】Serverless Kubernetes 集群支持创建GPU容器实例** 

    在 Serverless Kubernetes 集群中创建使用GPU容器应用。您可以通过使用模板创建功能，在YAML文件里为Pod指定为GPU类型即可。详情请参见[使用GPU容器实例](../../../../dita-oss-bucket/SP_235/DNcsk1894097/ZH-CN_TP_220322_V1.md#)。

-   **【新地域】容器镜像服务企业版开放北京区域** 

    即日起，使用镜像服务企业版在华北2北京区域上线，欢迎使用。详情请参见。

-   **【新功能】FPGA集群上线，支持视频图像加速** 

    容器服务推出FPGA集群，该集群以FPGA F3实例为工作节点，适用于视频编码H265、图片转码JPEG-to-HEIF等计算业务。过去需要一周或者更长时间才能完成的FPGA视频加速方案，如今短时间内开箱即用，大幅节省码率与优化同画质视频的CDN带宽成本。您可以通过控制台**集群** \> **集群**创建集群时，在集群模板选择选择**FPGA专有集群**进行操作。


## 2019年3月 {#section_bx3_ngf_fnv .section}

-   **【新地域】Kubernetes 托管版新增张家口、呼和浩特、美西、法兰克福四个开服区域** 

    Kubernetes托管版新增张家口、呼和浩特、美西、法兰克福四个区域，用户可以在以上区域使用Kubernetes托管版。

-   **【新地域】容器镜像服务-企业版新增上海区域；容器镜像服务-共享版国际站全区域开放** 

    容器镜像服务-企业版在3月21日阿里云峰会上正式亮相，企业版具备更强的安全及镜像分发能力。目前该功能在上海处于公测状态，想要体验的用户可通过提交工单申请。此外，共享版已在国际站全区域开放。详请请参见[什么是容器镜像服务企业版](https://help.aliyun.com/document_detail/111958.html)[什么是容器镜像服务](https://www.alibabacloud.com/help/zh/doc-detail/60945.html)。

-   **【新功能】Kubernetes 版本升级到 1.12.6** 

    容器服务ACK 的 Kubernetes 版本全面升级到 1.12.6，用户可以通过控制台创建 1.12 版本的 Kubernetes 集群。

-   **【新功能】Kubernetes托管版支持SLS日志插件** 

    Kubernetes托管版集群支持日志服务的插件，和专有Kubernetes集群一样，用户可以在创建集群时选择使用日志服务，享受日志服务对Kubernetes日志的强大管理能力。详情请参见[创建Kubernetes 托管版集群](../../../../dita-oss-bucket/SP_235/DNcsk1877477/ZH-CN_TP_40726.md#)。

-   **【新功能】Kubernetes托管版支持Windows容器集群** 

    即日起，使用镜像服务企业版在华北2北京区域上线。欢迎使用。详情请参见[创建Kubernetes 托管版集群](../../../../dita-oss-bucket/SP_235/DNcsk1877477/ZH-CN_TP_40726.md#)。

-   **【新功能】ACK支持IPVS** 

    容器服务ACK已开放IPVS的代理模式，IPVS不同于传统的Iptables模式，在大规模集群中会显著提高负载均衡的性能，用户可以在所有集群所有区域中使用该功能。

-   **【新功能】集群模板** 

    容器服务ACK控制台上线集群模板新功能。用户可以通过集群模板按照业务场景选择不同种类的Kubernetes集群，例如托管集群、神龙集群、GPU集群、Windows集群等。集群模板将帮助用户更加快速便捷地创建适合自己的Kubernetes集群。

-   **【新功能】Serverless Kubernetes支持大规格弹性实例（ECI）** 

    Serverless Kubernetes集群新扩大规格ECI实例（从8vCPU扩大到64vCPU），支持基因计算等场景，其规格最大为64vCPU 256GiB，最小为0.25vCPU 0.5GiB，丰富的实例规格种类让用户在部署业务时有更多的选择，达到最佳的能效比。详细请参见[使用限制](https://www.alibabacloud.com/help/zh/doc-detail/89138.html)。


## 2019年2月 {#section_otf_fue_y8f .section}

-   **【新地域】Kubernetes托管版深圳上线** 

    用户可以在中国站和国际站的深圳区域使用Kubernetes托管版。

    使用Kubernetes托管版的核心优势有：

    -   节省资源，每个集群节省3个master节点。
    -   运维简单，容器服务 Kubernetes版负责帮助托管master节点。
    -   安全，容器服务 Kubernetes版护航满足用户安全需求。
    详情请参见[创建Kubernetes 托管版集群](../../../../dita-oss-bucket/SP_235/DNcsk1877477/ZH-CN_TP_40726_V5.md#)。

-   **【新功能】应用目录发布Knative Addon** 

    Knative是一种可缩放至零、请求驱动的计算运行环境，构建在 Kubernetes 和 Istio 之上，支持为 serverless 应用、函数提供部署与服务。

    容器服务 Kubernetes版推出Knative Addon插件，帮助用户能够基于容器服务 Kubernetes版集群快速搭建Knative Serving环境。详情请参见[概述](../../../../dita-oss-bucket/SP_235/DNcsk1877477/ZH-CN_TP_423001_V1.md#)。

-   **【新功能】智能运维集群检查功能在中国站全区域开放** 

    集群检查可以深度检查集群资源、组件、配置等，帮助用户快速定位集群使用问题。详情请参见[通过集群检查定位集群问题](../../../../dita-oss-bucket/SP_235/DNcsk1894097/ZH-CN_TP_159904_V1.md#)。


## 2019年1月 {#section_obn_4pc_hce .section}

-   **【新功能】Windows容器内测上线** 

    容器服务 Kubernetes版支持Windows容器，Windows下的应用也可以容器化运行在Kubernetes上，享受Kubernetes弹性调度管理所带来的优势。

    用户可以在容器服务 Kubernetes版的Kubernetes集群以及托管版Kubernetes集群中通过添加节点的方式来添加Windows节点。详细请参见[创建Windows Kubernetes 集群](../../../../dita-oss-bucket/SP_235/DNcsk1877477/ZH-CN_TP_149635_V1.md#)。

    目前该功能处于内测状态，想要体验的用户可通过提交工单申请。

-   **【新功能】容器镜像服务企业版内测上线** 

    与目前的免费镜像仓库不同，企业版为用户部署一整套构建在独立资源上的容器镜像仓库，提供企业级的镜像安全托管功能、更强的大规模镜像分发能力，以及稳定的镜像构建服务，适合拥有大规模集群节点并且安全需求较高的企业级客户。。

    目前该功能处于内测状态，想要体验的用户可通过提交工单申请。

-   **【新功能】集群智能运维上线** 

    智能运维的目的是用户提供不同场景下的容器集群使用的最优解决方案，该功能在杭州区域已上线。

    用户可以进行深度检查集群资源、组件、配置等信息，帮助用户快速定位集群使用问题。详情请参见[通过集群检查定位集群问题](../../../../dita-oss-bucket/SP_235/DNcsk1894097/ZH-CN_TP_159904_V1.md#)。

-   **【新功能】容器服务支持ARMS应用实时监控** 

    容器服务已集成ARMS业务实时监控服务的能力，用户在集群中安装相应的arms插件后，即可对集群中所部署的应用进行性能的监控。

    ARMS是一款针对 Java 应用的性能管理（APM）软件。无需修改任何代码，只需在 Java 应用的启动脚本中挂载一个探针，该探针就能够对用户的 Java 应用进行全方位监控，例如快速定位出错接口和慢接口、重现调用参数、检测内存泄漏、发现系统瓶颈等，从而大幅提升线上问题诊断问题的效率。详细请参见[接入容器服务 Kubernetes 版应用](https://www.alibabacloud.com/help/zh/doc-detail/103106.html)。

-   **【商用】Serverless Kubernetes底层所调度的容器实例ECI开始商用化收费** 

    2019年01月22日起，Serverless Kubernetes的底层所调度的ECI容器实例开始商用收费，用户在使用Serverless Kubernetes集群创建容器实例时会有计费产生。Serverless Kubernetes本身继续为用户提供免费服务。Serverless Kubernetes底层所调度的容器实例ECI收费价格标准参考[计费说明](https://www.alibabacloud.com/help/zh/doc-detail/89142.html)。

-   **【新地域】Serverless Kubernetes在中国站的北京、深圳上线** 

    Serverless Kubernetes集群可以在北京、深圳区域可以部署，享受无服务器容器带来的极致体验。


## 2018年12月 {#section_cel_imb_db2 .section}

-   **【新地域】容器服务英国（伦敦）上线** 

    用户可以在中国站和国际站的伦敦区域使用容器服务 Kubernetes版，享受Kubernetes容器带来的云原生能力。

-   **【新地域】Kubernetes托管版上海、马来西亚、印度上线** 

    用户可以在中国站和国际站的上海、马来西亚、印度区域使用托管版Kubernetes。

-   **【新功能】支持在集群中移除节点** 

    用户可以从集群中移除指定节点，移除同时并且可选择释放ECS与否，详细请参见[移除节点](../../../../dita-oss-bucket/SP_235/DNcsk1877477/ZH-CN_TP_79987.md#)。

-   **【新功能】支持开放守护进程集（DaemonSet）应用的部署** 

    用户可以创建DaemonSet类型的应用，DaemonSet是一种守护进程，可以在集群的每一个节点上有且只有一个Pod。

-   **【新功能】支持自定义Istio网关** 

    用户可以自定义Istio的入口和出口网关，支持通过不同参数定制化，详细请参见[自定义Istio网关](../../../../dita-oss-bucket/SP_235/DNcsk1894097/ZH-CN_TP_156901_V1.md#)。

-   **【新功能】支持Istio CoreDNS** 

    实现基于CoreDNS Plugin扩展实现Istio Service Entry的DNS寻址，详细请参见[Istio CoreDNS](../../../../dita-oss-bucket/SP_235/DNcsk1894097/ZH-CN_TP_159221_V1.md#)。

-   **【新功能】托管版Kubernetes集群功能增强，创建集群时支持使用已有ECS** 

    用户可以在创建托管版Kubernetes集群时直接选择已有的ECS节点，而不必新建节点。详细请参见[创建Kubernetes 托管版集群](../../../../dita-oss-bucket/SP_235/DNcsk1877477/ZH-CN_TP_40726.md#)。


## 2018年11月 {#section_xc9_nd0_9a6 .section}

-   **【新地域】Kubernetes托管版国际站印尼区上线** 

    用户可以在国际站的印尼区域使用容器服务 Kubernetes版，享受Kubernetes容器带来的云原生能力。

-   **【商用】网络插件Terway上线** 

    阿里云容器服务推出高性能网络插件Terway，支持容器直接通过ENI通信，性能比VPC Flannel更高。详细请参见[如何使用Terway网络插件](../../../../dita-oss-bucket/SP_235/DNcsk1877477/ZH-CN_TP_64408.md#)。

-   **【新功能】支持Worker节点性能指标缩略图显示** 

    支持Worker节点性能指标缩略图显示，更加方便用户查看节点的状况。

-   **【新功能】集群添加已有节点时支持批量添加** 

    集群添加已有节点时支持批量添加，还可以选择多台节点批量加入Kubernetes集群。

-   **【新功能】集群支持证书滚动** 

    集群支持证书滚动，防止证书过期。


## 2018年10月 {#section_u2s_ejq_3vh .section}

-   **【新地域】容器服务Kubernetes 金融云深圳上线** 

    容器服务Kubernetes可以在深圳金融云部署，享受金融云的安全合规保障。

-   **【新功能】容器服务Kubernetes 托管版国际站上线**
-   **【新功能】Kubernetes 应用管理优化，支持Deployment版本管理和回滚** 

    容器服务Kubernetes 应用管理优化，支持Deployment版本管理和回滚。

-   **【新功能】Istio Addon升级，深度集成Kubernetes** 

    支持Istio与容器服务Kubernetes集群深度集成。详细请参见Istio管理的[概述](../../../../dita-oss-bucket/SP_235/DNcsk1877477/ZH-CN_TP_18952.md#)。


## 2018年9月 {#section_az3_zie_off .section}

-   **【新功能】支持容器服务Kubernetes 支持1.11版本** 

    -   包括CRD增强、CoreDNS GA、Pod优先级和抢占式调度等特性。
    -   支持多版本，可以按需选择1.10或1.11。
    -   控制台支持多容器和有状态应用。
    详细请参见[镜像创建有状态StatefulSet应用](../../../../dita-oss-bucket/SP_235/DNcsk1877477/ZH-CN_TP_21663.md#)。

-   **【新功能】Kubernetes服务支持免密拉取阿里云镜像服务私有仓库** 

    容器服务Kubernetes支持免密拉取阿里云镜像服务私有仓库。

-   **【新功能】支持节点自动伸缩** 

    详细请参见[节点自动伸缩](../../../../dita-oss-bucket/SP_235/DNcsk1877477/ZH-CN_TP_19071.md#)。

-   **【新功能】支持竞价实例** 

## 2018年8月 {#section_mid_xaz_hp0 .section}

-   **【新功能】容器服务Kubernetes 托管版公测上线** 

    详细请参见[创建Kubernetes 托管版集群](../../../../dita-oss-bucket/SP_235/DNcsk1877477/ZH-CN_TP_40726.md#)。

-   **【新功能】Istio Addon发布**

## 2018年7月 {#section_jum_ss0_vo1 .section}

-   **【新地域】容器服务 Kubernetes 澳洲上线**
-   **【新功能】支持灰度和分批发布** 

    详见Kubernetes集群中通过 Ingress 实现灰度发布和蓝绿发布的[概述](../../../../dita-oss-bucket/SP_235/DNcsk1894097/ZH-CN_TP_15821.md#)以及[在阿里云容器服务Kubernetes上使用分批发布](../../../../dita-oss-bucket/SP_235/DNcsk1877477/ZH-CN_TP_17337.md#)。


## 2018年6月 {#section_02h_9jj_r5f .section}

-   **【新地域】容器服务Kubernetes 日本站、华北5上线**
-   **【新功能】容器服务Kubernetes 1.10版本支持FPGA、HugePages等特性**
-   **【新功能】容器服务Kubernetes监控增强，支持应用监控和报警** 

    详细请参见[与云监控集成与使用](../../../../dita-oss-bucket/SP_235/DNcsk1877477/ZH-CN_TP_15813.md#)。

-   **【新功能】容器服务Kubernetes支持直接创建包年包月集群** 

    详细请参见[创建Kubernetes集群](../../../../dita-oss-bucket/SP_235/DNcsk1877477/ZH-CN_TP_16639.md#)。

-   **【新功能】容器服务Serverless Kubernetes 发布对exec/attach和ingress的支持** 

    详细请参见[Kubernetes 功能支持](../../../../dita-oss-bucket/SP_235/DNcsk1877477/ZH-CN_TP_16472.md#)。


## 2018年5月 {#section_85h_vha_wxh .section}

-   **【新地域】容器服务Kubernetes 上海金融云上线** 

    容器服务Kubernetes可以在上海金融云部署，享受金融云的安全合规保障。

-   **【新功能】容器服务 Serverless Kubernetes 发布** 

    详细请参见[Serverless Kubernetes 集群](ZH-CN_TP_23961.md#)。

-   **【新功能】容器服务Kubernetes 支持蓝绿发布、灰度发布和AB测试** 

    详细请参见Kubernetes集群中通过 Ingress 实现灰度发布和蓝绿发布的[概述](../../../../dita-oss-bucket/SP_235/DNcsk1894097/ZH-CN_TP_15821.md#)。


## 2018年4月 {#section_im3_d4e_yvq .section}

-   **【新地域】容器服务Kubernetes 印度、印尼、迪拜地域上线** 

    容器服务Kubernetes已经在东南亚、中东和印度的5个地域全部上线，皆可使用最新稳定的1.9版本。

-   **【新功能】容器服务多可用区Kubernetes高可用集群发布** 

    支持将Master部署到3个可用区，而woker节点也可以根据需要选择部署在多个可用区。从而最大程度实现集群的生产可用。详情请参见[创建多可用区 Kubernetes 集群](../../../../dita-oss-bucket/SP_235/DNcsk1877477/ZH-CN_TP_16644.md#)。

-   **【新功能】容器服务Kubernetes 服务目录更新，支持MySQL、RDS、RabbitMQ和Spark** 

    详细请参见服务目录管理的[概述](../../../../dita-oss-bucket/SP_235/DNcsk1877477/ZH-CN_TP_15775.md#) 。

-   **【新功能】容器服务Kubernetes 应用目录更新，支持Helm Release管理** 

    详细请参见[基于Helm的发布管理](../../../../dita-oss-bucket/SP_235/DNcsk1877477/ZH-CN_TP_15770.md#)。


## 2018年3月 {#section_9kq_pud_jc9 .section}

-   **【新功能】容器服务Kubernetes 支持1.9版本，支持自定义ECS镜像** 

    容器服务支持原生Kubernetes 1.9.3版本，Workloads API正式发布，CRD默认开启，支持GPU调度。除此之外，在创建集群时，可选择自定义ECS镜像。在添加节点时，支持自动化重置镜像的方式加入。

-   **【新功能】容器服务Kubernetes 应用目录发布，支持通过Helm一键部署应用** 

    详细请参见[基于Helm的发布管理](../../../../dita-oss-bucket/SP_235/DNcsk1877477/ZH-CN_TP_15770.md#)。

-   **【新功能】容器服务Kubernetes 服务目录发布，支持ServiceBroker** 

    详细请参见服务目录管理的[概述](../../../../dita-oss-bucket/SP_235/DNcsk1877477/ZH-CN_TP_15775.md#)。

-   **【新功能】容器服务Kubernetes 资源监控增强，支持通过云监控提供节点监控** 

    详细请参见[通过资源分组进行监控与告警](../../../../dita-oss-bucket/SP_235/DNcsk1877477/ZH-CN_TP_15811.md#)。


## 2018年1月 {#section_4p1_b8k_71r .section}

-   **【新地域】容器服务Kubernetes 和容器镜像服务国际站上线**
-   **【新功能】容器服务Kubernetes支持1.8.4版本，并提供额外安全增强、弹性伸缩等功能** 

    详细请参见[节点自动伸缩](../../../../dita-oss-bucket/SP_235/DNcsk1877477/ZH-CN_TP_19071.md#)。

-   **【新功能】容器服务Kubernetes FlexVolume数据卷发布，支持云盘、NAS和OSS** 

    详细请参见[使用阿里云云盘](../../../../dita-oss-bucket/SP_235/DNcsk1877477/ZH-CN_TP_16726.md#)、[使用阿里云 NAS](../../../../dita-oss-bucket/SP_235/DNcsk1877477/ZH-CN_TP_18764.md#)和[使用阿里云 OSS](../../../../dita-oss-bucket/SP_235/DNcsk1877477/ZH-CN_TP_16689.md#)。

-   **【新功能】容器服务Kubernetes 网络功能增强，支持Network Policy和带宽限制** 

    详细请参见[通过负载均衡（Server Load Balancer）访问服务](../../../../dita-oss-bucket/SP_235/DNcsk1877477/ZH-CN_TP_16677.md#)。

-   **【新功能】容器服务Kubernetes 支持弹性裸金属服务器**

## 2017年10月 {#section_g57_75x_yg0 .section}

-   **【新功能】原生Kubernetes新版本上线** 

    容器服务Kubernetes 支持1.8.1版本。请参见[什么是容器服务 ACK](ZH-CN_TP_15496.md#)

-   **【新功能】区块链解决方案公测** 

    详细请参见区块链解决方案的[概述](../../../../dita-oss-bucket/SP_235/DNcsk1884167/ZH-CN_TP_16614.md#)。


## 2017年8月 {#section_x9v_wqd_jq9 .section}

**【新功能】Kubernetes解决方案支持Kubernetes 1.7.2版本**

