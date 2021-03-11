---
keyword: [Ingress-tracing, 链路追踪, 调用链路]
---

# 使用Ingress-tracing实现链路追踪

ACK提供了Ingress-tracing链路追踪功能，通过Ingress-tracing链路追踪功能可以接入到链路追踪控制台中。您可以在链路追踪控制台查看调用链路、链路拓扑等。本文介绍如何使用Ingress-tracing实现链路追踪。

-   [开通相关服务并授权](/cn.zh-CN/准备工作/开通相关服务并授权.md)
-   [创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes托管版集群.md)

阿里云提供了链路追踪服务，为分布式应用的开发者提供了完整的调用链路还原、调用请求量统计、链路拓扑等，能够帮助快速分析和诊断分布式应用架构下的性能瓶颈，提高微服务时代下的开发诊断效率。ACK提供的Ingress-Nginx-Controller支持集成链路追踪服务，您可以根据需求开启该功能，查看链路追踪数据。

## 步骤一：获取接入点信息

获取接入点信息，根据实际情况选择客户端采集工具，本文以Zipkin为例。

1.  登录[链路追踪Tracing Analysis控制台](https://tracing.console.aliyun.com/)。

2.  在控制台页面顶部选择地域。

    **说明：** 地域需要与[步骤二：在Ingress中开启链路追踪服务](#section_1sj_zks_0mv)的ACK集群保持一致。

3.  在左侧导航栏单击**集群配置**。

4.  在集群配置页面上单击**接入点信息**页签，在集群信息区域打开**显示Token**开关。

5.  在客户端采集工具区域单击**Zipkin**。

6.  在下方表格的相关信息列中，单击接入点信息末尾的复制图标。

    **说明：** 建议选择v1版本接入点。

    ![接入点信息-zipkin](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7422345161/p188458.png)


## 步骤二：在Ingress中开启链路追踪服务

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**配置管理** \> **配置项**。

5.  在配置项页面名称搜索框中搜索nginx-configuration，找到nginx-configuration，然后单击nginx-configuration**操作**列的**编辑**。

6.  配置链路追踪方式。

    根据使用的链路追踪方式，需要配置不同的字段，本文采用zipkin来提供链路追踪服务。

    在**编辑**面板单击**添加**，设置**名称**为zipkin-collector-host ，**值**为[步骤一：获取接入点信息](#section_8f8_sdn_c03)获取的接入点信息。

    **说明：** 该接入点信息需要去掉HTTP，并在末尾加上问号。例如接入点信息为`http://tracing-analysis-dc-hz-internal.aliyuncs.com/adapt_******_******/api/v1/spans`，则输入的值为`tracing-analysis-dc-hz-internal.aliyuncs.com/adapt_******_******/api/v1/spans？`。

7.  开启链路追踪服务。

    单击**添加**，设置**名称**为enable-opentracing ，**值**为true。然后单击**确定**。


## 步骤三：查看调用链路数据

1.  登录[链路追踪Tracing Analysis控制台](https://tracing.console.aliyun.com/)。

2.  在控制台左侧导航栏中单击**应用列表**。

3.  在应用列表页面顶部选择地域，然后单击应用名称。

4.  在应用详情页面左侧导航栏中单击**接口调用**，在左侧的接口列表中单击调用链路页签，调用链路页签列出了该应用耗时最长的至多100个调用链路。关于更多链路数据，请参见[查看接口调用情况](/cn.zh-CN/控制台操作/应用管理/查看接口调用情况.md)。

    ![调用链路](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1346211161/p227287.png)


