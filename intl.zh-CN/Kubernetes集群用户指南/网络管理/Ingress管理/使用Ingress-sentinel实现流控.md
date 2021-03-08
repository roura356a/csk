---
keyword: [Nginx, 流控功能, 高可用防护功能]
---

# 使用Ingress-sentinel实现流控

ACK提供了Ingress-sentinel流控功能，通过Ingress-sentinel流控功能可以快速接入到AHAS中。当有请求流量时，您可以在AHAS控制台中查看Nginx网关请求的实时QPS和RT等数据。本文介绍如何使用Ingress-sentinel功能实现流控。

-   [开通AHAS]()
-   [创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes托管版集群.md)

应用高可用服务AHAS（Application High Availability Service）提供了应用架构探测感知，故障注入式高可用能力评测和流控降级高可用防护三大核心能力，其中针对Nginx的流控降级高可用防护功能已集成到ACK提供的Ingress-Nginx-Controller中，您可用根据实际需求开启该功能，查看流控数据。

## 使用限制

-   仅支持在深圳、北京、上海、张家口、杭州五个地区使用Ingress-sentinel流控功能。
-   开启Ingress-sentinel流控功能，会消耗一定的系统资源。如果您使用的是4核8 GB机器，建议按照以下要求配置流控规则：
    -   QPS上限为10000。
    -   流控的规则数（即关注的流控对象，默认是Host ）上限为2000。

        您可以在[AHAS控制台](https://ahas.console.aliyun.com)对流控规则数做详细的配置，支持域名、正则表达式等。

    -   请求接口的RT值不小于10 ms。

## 开启Ingress-sentinel流控功能

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**配置管理** \> **配置项**。

5.  在配置项页面名称搜索框中搜索nginx-configuration，找到nginx-configuration，然后单击nginx-configuration**操作**列的**编辑**。

6.  自定义应用名称，方便区分。

    在**编辑**面板单击**添加**，设置**名称**为sentinel-params ，**值**为--app=demo-k8s-2020。

    **说明：** --app=demo-k8s-2020字段中demo-k8s-2020为应用名称，您可以根据实际需求替换应用名称。

7.  开启Ingress-sentinel流控功能。

    单击**添加**，设置**名称**为use-sentinel，**值**为true。然后单击**确定**。


## 配置流控规则

登录[AHAS控制台](https://ahas.console.aliyun.com)，在控制台左侧导航栏选择**流量防护** \> **Nginx防护**，单击目标应用卡片，根据实际需求配置流控规则。具体操作请参见[配置流控规则]()、[配置隔离规则]()或[配置熔断规则]()。

规则配置完成后，您可以在应用概览页面实时查看流控指标。

![流控指标](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3105911161/p227294.png)

