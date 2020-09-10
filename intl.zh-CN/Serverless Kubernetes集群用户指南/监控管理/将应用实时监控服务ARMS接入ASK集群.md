---
keyword: [ASK集群, ASK ARMS, ASK接入应用实时监控服务ARMS]
---

# 将应用实时监控服务ARMS接入ASK集群

阿里云Serverless Kubernetes（ASK）集群接入应用实时监控服务ARMS能为分布在各处的Kubernetes集群提供统一的管理方式。本文介绍如何通过容器服务Kubernetes版中的应用将应用实时监控服务ARMS接入至标准的Serverless集群。

[创建Serverless Kubernetes集群](/intl.zh-CN/Serverless Kubernetes集群用户指南/快速入门/创建Serverless Kubernetes集群.md)

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

3.  在**应用目录**页面单击**阿里云应用**页签，选中**ack-arms-pilot**应用。

    **阿里云应用**包含较多应用，您可在页面右上角搜索**ack-arms-pilot**，支持关键字搜索。

4.  在**应用目录 - ack-arms-pilot**页面右侧**创建**区域，选择目标集群。

5.  在**应用目录 - ack-arms-pilot**页面单击**参数**页签，设置相应的参数，然后单击右侧**创建**区域的**创建**。

    ![pilot parm](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/9206659951/p76784.png)

    |参数|描述|
    |--|--|
    |**accessKey**|您的阿里云AccessKey ID。AK权限需包含访问ARMS的权限。|
    |**accessKeySecret**|您的阿里云AccessKey Secret。|

    **说明：**

    -   如果您的集群和专有网络VPC之间有专线，专线会被自动使用。
    -   如果您是通过公网注册的外部集群，需要删除镜像参数中的vpc。

验证安装是否成功，请参见[为容器服务Kubernetes版Java应用安装探针](/intl.zh-CN/应用监控/开始监控 Java 应用/为容器服务Kubernetes版Java应用安装探针.md)。

有关如何使用ARMS，请参见[ARMS应用总览](/intl.zh-CN/应用监控/控制台功能/应用总览.md)。

[注册外部集群简介](/intl.zh-CN/Kubernetes集群用户指南/多云混合云管理/注册外部集群简介.md)

  


