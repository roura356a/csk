---
keyword: SLB Ingress
---

# SLB Ingress概述

本文介绍Ingress基本概念、Ingress Controller工作原理和SLB Ingress Controller使用说明。

## Ingress基本概念

在Kubernetes集群中，Ingress作为集群内服务对外暴露的访问接入点，其几乎承载着集群内服务访问的所有流量。Ingress是Kubernetes中的一个资源对象，用来管理集群外部访问集群内部服务的方式。您可以通过Ingress资源来配置不同的转发规则，从而达到根据不同的规则设置访问集群内不同的Service后端Pod。

## Ingress Controller工作原理

为了使得Ingress资源正常工作，集群中必须要有个Ingress Controller来解析Ingress的转发规则。Ingress Controller收到请求，匹配Ingress转发规则转发到后端Service，而Service转发到Pod，最终由Pod处理请求。Kubernetes中Service、Ingress与Ingress Controller有着以下关系：

-   Service是后端真实服务的抽象，一个Service可以代表多个相同的后端服务。
-   Ingress是反向代理规则，用来规定HTTP/HTTPS请求应该被转发到哪个Service上。例如根据请求中不同的Host和URL路径，让请求落到不同的 Service上。
-   Ingress Controller是一个反向代理程序，负责解析Ingress的反向代理规则。如果Ingress有增删改的变动，Ingress Controller会及时更新自己相应的转发规则，当Ingress Controller收到请求后就会根据这些规则将请求转发到对应的Service。

Ingress Controller通过API Server获取Ingress资源的变化，动态地生成Load Balancer所需的的配置文件，然后依次生成新的路由转发规则。

![工作原理](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8014073161/p207789.png)

## SLB Ingress Controller使用说明

**说明：** 为Ingress服务的SLB是由Controller完全托管的，您不能自己在SLB控制台上面进行配置，否则可能造成Ingress服务的异常。

阿里云Serverless Kubernetes集群，基于[基于域名或URL路径进行转发](/intl.zh-CN/CLB教程专区/基于域名或URL路径进行转发.md)提供了托管的SLB Ingress Controller ，您无需承担额外的IaaS成本，云产品级别的容灾能力给您提供稳定的Ingress服务。

-   在不指定SLB实例情况下，ASK会自动生成一个公网SLB实例。ASK不会自动生成私网SLB实例。
-   公网SLB和私网SLB都只能通过Annotation指定SLB ID。
    -   如果需要使用公网SLB提供服务，您可以使用已有的公网SLB实例或创建一个公网SLB实例，然后通过Annotation指定SLB ID。
    -   如果需要使用私网SLB提供服务，您可以使用已有的私网SLB实例或创建一个私网SLB实例，然后通过Annotation指定SLB ID。
-   当您指定使用已存在的SLB实例时，要求该SLB实例规格必须是性能保障型（支持ENI），同时确保80和443端口当前没有其他服务使用。
-   SLB实例前端监听会把80端口设置成HTTP协议、把443端口设置成 HTTPS协议。
-   SLB默认会使用第一个（按时间正序排序）Ingress指定的**secret**证书作为HTTPS监听证书，如果所有Ingress都没有指定**secret**，则使用**fake**的默认证书。

**相关文档**  


[通过Ingress提供7层服务访问](/intl.zh-CN/Serverless Kubernetes集群用户指南/网络管理/通过Ingress提供7层服务访问.md)

[Nginx Ingress概述](/intl.zh-CN/Serverless Kubernetes集群用户指南/网络管理/Nginx Ingress管理/Nginx Ingress概述.md)

