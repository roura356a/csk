---
keyword: ALB Ingress
---

# ALB Ingress概述

本文介绍Ingress基本概念、Ingress Controller工作原理和ALB Ingress Controller使用说明。

## Ingress基本概念

在Kubernetes集群中，Ingress作为集群内服务对外暴露的访问接入点，其几乎承载着集群内服务访问的所有流量。Ingress是Kubernetes中的一个资源对象，用来管理集群外部访问集群内部服务的方式。您可以通过Ingress资源来配置不同的转发规则，从而达到根据不同的规则设置访问集群内不同的Service后端Pod。

## Ingress Controller工作原理

为了使得Ingress资源正常工作，集群中必须要有个Ingress Controller来解析Ingress的转发规则。Ingress Controller收到请求，匹配Ingress转发规则转发到后端Service，而Service转发到Pod，最终由Pod处理请求。Kubernetes中Service、Ingress与Ingress Controller有着以下关系：

-   Service是后端真实服务的抽象，一个Service可以代表多个相同的后端服务。
-   Ingress是反向代理规则，用来规定HTTP/HTTPS请求应该被转发到哪个Service上。例如根据请求中不同的Host和URL路径，让请求落到不同的 Service上。
-   Ingress Controller是一个反向代理程序，负责解析Ingress的反向代理规则。如果Ingress有增删改的变动，Ingress Controller会及时更新自己相应的转发规则，当Ingress Controller收到请求后就会根据这些规则将请求转发到对应的Service。

Ingress Controller通过API Server获取Ingress资源的变化，动态地生成Load Balancer所需的配置文件，然后依次生成新的路由转发规则。

![工作原理](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/8014073161/p207789.png)

## ALB Ingress Controller使用说明

**说明：** 为Ingress服务的ALB是由Controller完全托管的，您不能自己在ALB控制台上面进行配置，否则可能造成Ingress服务的异常。

ALB Ingress基于阿里云应用型负载均衡 ALB（Application Load Balancer） 之上提供更为强大的Ingress流量管理方式，兼容Nginx Ingress，具备处理复杂业务路由和证书自动发现的能力，支持HTTP、HTTPS和QUIC协议，完全满足在云原生应用场景下对超强弹性和大规模七层流量处理能力的需求。

**相关文档**  


[通过ALB Ingress访问服务](/intl.zh-CN/Serverless Kubernetes集群用户指南/网络/ALB Ingress管理/通过ALB Ingress访问服务.md)

[ALB Ingress Controller组件管理](/intl.zh-CN/Serverless Kubernetes集群用户指南/网络/ALB Ingress管理/ALB Ingress Controller组件管理.md)

