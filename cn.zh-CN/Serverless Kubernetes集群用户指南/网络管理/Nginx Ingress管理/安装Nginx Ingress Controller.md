---
keyword: 安装Nginx Ingress Controller
---

# 安装Nginx Ingress Controller

阿里云Serverless Kubernetes（ASK）官方基于社区版的Nginx Ingress Controller进行了优化，提供Nginx Ingress Controller功能。本文介绍如何在ASK集群安装Nginx Ingress Controller。

## 操作步骤

**方式一：创建集群时安装Nginx Ingress Controll**

创建ASK集群时，在**Ingress**参数配置区域，选择安装**Nginx Ingress** 。具体操作，请参见[创建Serverless Kubernetes集群](/cn.zh-CN/Serverless Kubernetes集群用户指南/快速入门/创建Serverless Kubernetes集群.md)。

**说明：** 安装Nginx Ingress Controller需要满足以下两个条件：

-   2个2核4 GiB的ECI实例：用于部署服务。
-   1个SLB：SLB实例类型可以是公网或者私网，SLB实例规格您可以按需选择。

![S8](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7040674161/p245306.png)

**方式二：在组件管理页面安装Nginx Ingress Controll**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**运维管理** \> **组件管理**。

5.  单击**网络**，在**Nginx Ingress Controller**组件区域单击**安装**。


