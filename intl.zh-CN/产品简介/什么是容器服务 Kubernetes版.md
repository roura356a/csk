# 什么是容器服务 Kubernetes版 {#concept_cbm_1zc_l2b .concept}

容器服务Kubernetes版（Container Service for Kubernetes）提供高性能可伸缩的容器应用管理服务，支持企业级Kubernetes容器化应用的生命周期管理。容器服务 Kubernetes 版简化集群的搭建和扩容等运维工作，整合阿里云虚拟化、存储、网络和安全能力，打造云端最佳的 Kubernetes 容器化应用运行环境。阿里云容器服务是全球首批通过Kubernetes一致性认证的云平台服务，也是Kubernetes认证服务供应商，可以为您提供专业的容器支持和服务。

容器服务Kubernetes版包含了专有版Kubernetes（Dedicated Kubernetes）、托管版Kubernetes（Managed Kubernetes）、Serverless Kubernetes三种形态，方便您按需选择。

-   专有版Kubernetes：需要创建3个Master（高可用）节点及若干Worker节点，可对集群基础设施进行更细粒度的控制，需要自行规划、维护、升级服务器集群。
-   托管版Kubernetes：只需创建Worker节点，Master节点由容器服务创建并托管。具备简单、低成本、高可用、无需运维管理Kubernetes集群Master节点的特点，您可以更多关注业务本身。
-   Serverless Kubernetes：无需创建和管理Master节点及Worker节点，即可通过控制台或者命令配置容器实例的资源、指明应用容器镜像以及对外服务的方式，直接启动应用程序。

## 产品功能 {#section_ts5_5jb_n2b .section}

**集群管理**

-   通过控制台一键创建专有版Kubernetes集群、托管版Kubernetes集群、Serverless Kubernetes集群，支持GPU实例和裸金属服务器，支持创建跨可用区高可用的集群。
-   提供容器优化的OS镜像，提供**稳定测试和安全加固**的Kubernetes和Docker版本。
-   支持多集群管理，支持跨可用区高可用集群，支持集群联邦管理。

**一站式容器生命周期管理**

-   **网络**

    提供针对阿里云优化的高性能VPC/ENI网络插件性能优于普通网络方案20%。

    支持容器访问策略和流控限制。

-   **存储**

    支持阿里云云盘、文件存储NAS、对象存储OSS，提供标准的FlexVolume驱动。

    支持存储卷动态创建，迁移。

-   **日志**

    支持高性能日志自动采集和阿里云日志服务集成。

    支持和第三方开源日志解决方案集成。

-   **监控**

    支持容器级别和VM级别的监控。您还可以和第三方开源监控解决方案进行集成。

-   **权限**

    支持集群级别的RAM授权管理。

    支持应用级别的权限配置管理。

-   **应用管理**

    支持灰度发布，支持蓝绿发布。

    支持应用监控，应用弹性伸缩。

    内置应用商店，支持Helm应用一键部署；支持服务目录，简化云服务集成。


**高可用调度策略，轻松打通上下游交付流程**

-   支持服务级别的亲和性策略和横向扩展。
-   支持跨可用区高可用和灾难恢复。
-   支持集群和应用管理的 OpenAPI，轻松对接持续集成和私有部署系统。

