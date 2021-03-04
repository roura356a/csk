---
keyword: [云原生AI, 监控, GPU资源]
---

# 云原生AI监控

云原生AI的监控组件能够帮助您监控集群的GPU资源使用情况以及集群的各命名空间下的资源配额使用情况。本文介绍如何安装和使用云原生AI的监控组件。

-   ACK的Kubernetes集群版本大于等于1.18.8。
-   Arena组件版本大于等于0.7.0。具体操作，请参见[通过组件安装最新版的Arena](/cn.zh-CN/解决方案/AI解决方案/环境准备/通过组件安装最新版的Arena.md)。
-   安装ARMS Prometheus监控组件，具体操作，请参见[arms-prometheus](https://cs.console.aliyun.com/#/k8s/catalog/detail/incubator_ack-arms-prometheus)。
-   安装AI-Dashboard组件。具体操作，请参见[安装云原生AI套件及访问AI Dashbaord](/cn.zh-CN/云原生AI用户指南/安装云原生AI套件及访问AI Dashbaord.md)。

## 安装云原生AI监控组件

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在左侧导航栏，选择**应用** \> **AI工程加速（公测）**。

5.  安装监控组件。

    -   如果您之前已经部署过云原生AI套件：

        在云原生AI套件页面，找到云原生AI监控组件**ack-alibaba-cloud-metrics-adapter**，然后单击右侧**操作**列下的**部署**。

    -   如果您之前未部署过云原生AI套件：

        在云原生AI套件页面，单击**一键部署**，然后在**监控**区域，选中**监控组件**后，单击**部署云原生AI套件**。


## 使用云原生AI监控组件查看GPU资源及不同命名空间下的资源配额

**说明：** 使用云原生AI监控组件前，您需要安装**ack-ai-dashboard**组件并且设置AI-Dashboard访问配置。关于如何安装和访问AI-Dashboard，请参见[安装云原生AI套件及访问AI Dashbaord](/cn.zh-CN/云原生AI用户指南/安装云原生AI套件及访问AI Dashbaord.md)。

进入云原生AI Dashboard页面后，可以进行以下操作：

-   登录云原生AI Dashboard后，系统默认呈现集群维度的资源信息。您可以查看集群相关的资源，例如节点个数、运行Job数、GPU使用率等。
-   在云原生AI Dashboard页面右上角，单击**Nodes**查看节点GPU资源信息。

    ![nodes](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6306672161/p240389.png)

-   在云原生AI Dashboard页面右上角，单击**TrainingJobs**查看Training Job的监控信息。
-   在云原生AI Dashboard页面右上角，单击**Quota**查看集群各命名空间的资源配额使用情况。

