# 容器服务Kubernetes集群三种形态对比 {#concept_om5_1wl_5gb .concept}

本文将介绍容器服务Kubernetes集群以下三种形态的对比情况：专有版Kubernetes（Dedicated Kubernetes）、托管版Kubernetes（Managed Kubernetes）、Serverless Kubernetes。

## 特点 {#section_pwx_mwl_5gb .section}

容器服务Kubernetes版（简称 ACK）提供高性能可伸缩的云原生应用管理能力，支持企业级Kubernetes容器化应用的全生命周期管理，并提供了以下三种Kubernetes集群形态：

-   专有版Kubernetes

    需要创建3个Master（高可用）节点及若干Worker节点，可对集群基础设施进行更细粒度的控制，需要自行规划、维护、升级服务器集群。

-   托管版Kubernetes

    只需创建Worker节点，Master节点由容器服务创建并托管。具备简单、低成本、高可用、无需运维管理Kubernetes集群Master节点的特点，您可以更多关注业务本身。

-   Serverless Kubernetes

    无需创建和管理Master节点及Worker节点，即可通过控制台或者命令配置容器实例的资源、指明应用容器镜像以及对外服务的方式，直接启动应用程序。


## 收费方式 {#section_wps_vwl_5gb .section}

-   专有版Kubernetes

    免费。用户承担Master节点和Worker节点的资源费用。

-   托管版Kubernetes

    免费。用户承担Worker节点的资源费用。

-   Serverless Kubernetes

    按容器实例的使用资源量和时长（秒）计费。


## 应用场景 {#section_zfk_1xl_5gb .section}

-   专有版Kubernetes

    适用于所有场景。

-   托管版Kubernetes

    适用于所有场景。

-   Serverless Kubernetes

    适用于批量任务、突发扩容，以及CI/CD测试。


## 三种集群形态的用户画像 {#section_vfk_2xl_5gb .section}

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/125425/155048688938882_zh-CN.png)

## 集群创建流程对比 {#section_t1y_2xl_5gb .section}

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/125425/155048688938883_zh-CN.png)

