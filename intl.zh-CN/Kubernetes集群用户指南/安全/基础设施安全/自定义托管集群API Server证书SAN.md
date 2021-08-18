---
keyword: [自定义API Server证书SAN, SAN（Subject Alternative Name）]
---

# 自定义托管集群API Server证书SAN

ACK集群的API Server服务端证书中SAN（Subject Alternative Name）字段默认包括集群本地域名、API Server负载均衡的内网IP、API Server服务本地IP和公网EIP等字段。如果您有特殊的代理访问或跨域访问需求，可以通过自定义SAN字段实现。本文介绍新建集群如何自定义API Server证书SAN和已有集群如何自定义更新API Server证书SAN。

目前支持Kubernetes托管版集群、ACK Pro版集群及ASK集群。以下仅枚举三种托管集群的创建方法，您只需要创建以下集群中的任意一种：

-   [创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)
-   [创建ACK Pro版集群](/intl.zh-CN/Kubernetes集群用户指南/ACK Pro集群/创建ACK Pro版集群.md)
-   [ASK使用快速入门](/intl.zh-CN/Serverless Kubernetes集群用户指南/集群/创建Serverless Kubernetes集群.md)

SAN是一项对X.509标准的扩展，它允许在SSL安全证书中使用`subjectAltName`字段将多种值（包括IP地址、域名、URI和电子邮件等）与证书关联。

## 自定义新建集群API Server证书SAN

本文以创建Kubernetes托管版集群为例说明新建集群如何自定义API Server证书SAN。

在创建集群的**集群配置**步骤，单击**显示高级选项**。在**自定义证书SAN**配置项中输入配置字段。具体操作，请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。

**说明：** **自定义证书SAN**字段的配置，支持输入符合规范的自定义IP、域名和URI，多个字段以英文逗号（,）分隔。

![an2](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/0255017161/p256859.png)

上图中的**自定义证书SAN**字段中包含两个域名和一个IP。

## 更新已有集群API Server证书SAN

**说明：** 更新或修改自定义证书SAN会触发集群API Server短暂重启，请您在业务非高峰时段操作。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群信息管理页面选择**基本信息**页签，单击**自定义证书SAN**右侧的**更新**。

5.  在**更新自定义证书SAN**对话框中，配置**自定义证书SAN**字段。

6.  参数配置完成后，单击**确定**。


