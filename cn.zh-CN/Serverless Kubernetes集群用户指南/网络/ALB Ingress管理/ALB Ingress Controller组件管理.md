---
keyword: 安装ALB Ingress Controller
---

# ALB Ingress Controller组件管理

阿里云Serverless Kubernetes集群，基于ALB七层转发规则提供了托管的ALB Ingress Controller。本文介绍如何在ASK集群安装和卸载ALB Ingress Controller。

## 安装ALB Ingress Controller

**方式一：创建集群时安装ALB Ingress Controller**

创建ASK集群时，在**Ingress**参数配置区域，选择安装**ALB Ingress**。具体操作，请参见[创建Serverless Kubernetes集群](/cn.zh-CN/Serverless Kubernetes集群用户指南/快速入门/创建Serverless Kubernetes集群.md)。

![12](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0219637261/p298251.png)

**方式二：在组件管理页面安装ALB Ingress Controller**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**运维管理** \> **组件管理**。

5.  单击**其他**页签，在**ALB Ingress Controller**组件区域单击**安装**。


## 卸载ALB Ingress Controller

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**运维管理** \> **组件管理**。

5.  单击**其他**页签，在ALB Ingress Controller组件区域单击**卸载**。


