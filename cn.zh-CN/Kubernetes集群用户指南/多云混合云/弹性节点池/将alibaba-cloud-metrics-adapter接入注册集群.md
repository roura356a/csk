---
keyword: [注册的Kubernetes集群, HPA, alibaba-cloud-metrics-adapter]
---

# 将alibaba-cloud-metrics-adapter接入注册集群

您可以通过alibaba-cloud-metrics-adapter使用云指标进行Pod的水平伸缩（HPA）。本文介绍如何通过容器服务Kubernetes版中的应用将alibaba-cloud-metrics-adapter接入至注册的Kubernetes集群。

您已通过容器服务Kubernetes版接入一个注册的Kubernetes集群。具体操作步骤，请参见[创建注册集群并接入本地数据中心集群](/cn.zh-CN/Kubernetes集群用户指南/多云混合云/混合集群/创建注册集群并接入本地数据中心集群.md)。

在Kubernetes中，监控数据除了用来展现具体的指标和数据，还有一个重要的用途就是通过HPA进行数据消费。为了满足开发者不同层次的监控需求，Kubernetes定义了三种不同的监控数据接口，分别是Resource Metric、Custom Metric以及External Metric：

-   Resource Metric通常是通过Metrics-Server进行采集，提供的主要是Pod、Node、Namespcae等Kubernetes中内置逻辑对象的监控。
-   Custom Metric是您自定义的监控指标，通常是通过Prometheus进行采集，然后再HPA进行消费。
-   External Metric主要针对外部指标，通常是面向云场景，例如您可以通过云厂商的External Metric来获取Ingress的QPS（每秒查询率）作为弹性指标。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

3.  在**应用目录**页面单击**阿里云应用**页签，选中**ack-alibaba-cloud-metrics-adapter**应用。

    **阿里云应用**包含较多应用，您可在页面右上角搜索**ack-alibaba-cloud-metrics-adapter**，支持关键字搜索。

4.  在**应用目录 - ack-alibaba-cloud-metrics-adapter**页面右侧的**创建**区域，选择目标集群。

    **命名空间**和**发布名称**为默认值，无需设置。

5.  在**应用目录 - ack-alibaba-cloud-metrics-adapter**单击**参数**页签，设置相应的参数，然后单击右侧的**创建**区域的**创建**。

    ![metric adapter](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3306659951/p103772.png)

    |参数|描述|
    |--|--|
    |**AccessKeyId**|您的阿里云AccessKeyId。|
    |**AccessKeySecret**|您的阿里云AccessKeySecret。|
    |**Region**|您集群所在的地域，例如cn-qingdao、ap-southeast-1。|

    **说明：** 如果您的集群和专有网络VPC之间有专线，专线会被自动使用。


**相关文档**  


[注册集群概述](/cn.zh-CN/Kubernetes集群用户指南/多云混合云/混合集群/注册集群概述.md)

[使用Ingress指标进行弹性伸缩](https://yq.aliyun.com/articles/713515)

