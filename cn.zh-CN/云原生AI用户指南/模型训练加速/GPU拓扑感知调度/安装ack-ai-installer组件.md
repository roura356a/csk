---
keyword: [GPU拓扑感知调度, Kubernetes调度]
---

# 安装ack-ai-installer组件

本文主要介绍在激活GPU拓扑资源调度前，您需要安装的组件和部署的配置。

-   创建Kubernetes Pro集群。Kubernetes Pro集群的实例规格类型需要设置为**异构计算GPU**，其它操作配置，请参见[创建Kubernetes Pro版集群](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/创建Kubernetes Pro版集群.md)。

    **说明：** 目前仅支持Kubernetes Pro集群，如果是专有版集群，请[提交工单](https://icms.alibaba-inc.com/content/csk/d360da?l=1&m=7459&n=2252239)申请白名单。

-   通过kubectl连接Kubernetes集群。具体操作，请参见[通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/连接集群/通过kubectl连接Kubernetes集群.md)。
-   系统组件版本要求具体如下表所示。

    |组件|版本要求|
    |--|----|
    |Kubernetes|1.18.8及以上版本|
    |Helm|3.0及以上版本|
    |Nvidia|418.87.01及以上版本|
    |训练框架NCCL版本|2.7+|
    |Docker|19.03.5|
    |操作系统|CentOS 7.6、CentOS 7.7、Ubuntu 16.04和Ubuntu 18.04、Alibaba Cloud Linux 2|
    |显卡|V100|


1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

3.  在应用目录页面的右上角**名称**文本框输入ack-ai-installer。

4.  在应用目录-ack-ai-installer页面右侧的**创建**面板中，在**集群**列表框中选择目标集群，并单击**创建**。![image](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8995975061/p183749.png)


[背景概述](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/GPU拓扑感知调度/GPU拓扑感知调度背景概述.md)

[分布式训练场景下激活GPU拓扑感知调度](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/GPU拓扑感知调度/分布式训练场景下激活GPU拓扑感知调度.md)

