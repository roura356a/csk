---
keyword: [Knative部署, Kubernetes]
---

# 一键部署Knative

Knative是一款基于Kubernetes的Serverless框架，其目标是制定云原生、跨平台的Serverless编排标准。本文介绍如何部署Knative。

[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes托管版集群.md)。

**说明：**

-   从Knative v0.14.0开始，仅支持Kubernetes版本1.15及以上的集群。支持标准托管、标准专有以及Serverless Kubernetes集群。
-   针对标准托管和标准专有Kubernetes集群，Worker节点数量需要大于等于3个。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

3.  在集群管理页左侧导航栏中，选择**应用** \> **Knative**。

4.  在组件管理页签，单击**一键部署Knative**。

5.  在**核心组件**区域，选择需要安装的Knative组件后，单击组件右侧**操作**列下的**部署**。

    -   **Tekton组件**：提供从源码到镜像的通用构建能力。
    -   **Serving组件**：管理Serverless工作负载，可以和事件结合并且提供了基于请求驱动的自动扩缩的能力，而且在没有服务需要处理的时候可以缩容到零个实例。
    -   **Eventing组件**：提供了事件的接入、触发等一整套事件管理的能力。

部署完成后，您可看到部署结果信息。

-   您可以单击**进入组件管理**，查看组件信息。
-   您可以单击**进入应用管理**，查看Knative应用相关操作。

![查看部署结果](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2895659951/p48854.png)

