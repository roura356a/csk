# Ingress概述

本文介绍Ingress基本概念、Ingress Controller工作原理和ACK的Ingress。

## Ingress基本概念

在Kubernetes集群中，Ingress作为集群内服务对外暴露的访问接入点，其几乎承载着集群内服务访问的所有流量。Ingress是Kubernetes中的一个资源对象，用来管理集群外部访问集群内部服务的方式。您可以通过Ingress资源来配置不同的转发规则，从而达到根据不同的规则设置访问集群内不同的Service后端Pod。

Ingress资源仅支持配置HTTP流量的规则，无法配置一些高级特性，例如负载均衡的算法、Sessions Affinity等，这些高级特性都需要在Ingress Controller中进行配置。

## Ingress Controller工作原理

为了使得Ingress资源正常工作，集群中必须要有个Ingress Controller来解析Ingress的转发规则。Ingress Controller收到请求，匹配Ingress转发规则转发到后端Service，而Service转发到Pod，最终由Pod处理请求。Kubernetes中Service、Ingress与Ingress Controller有着以下关系：

-   Service是后端真实服务的抽象，一个Service可以代表多个相同的后端服务。
-   Ingress是反向代理规则，用来规定HTTP/HTTPS请求应该被转发到哪个Service上。例如根据请求中不同的Host和URL路径，让请求落到不同的 Service上。
-   Ingress Controller是一个反向代理程序，负责解析Ingress的反向代理规则。如果Ingress有增删改的变动，Ingress Controller会及时更新自己相应的转发规则，当Ingress Controller收到请求后就会根据这些规则将请求转发到对应的Service。

Ingress Controller通过API Server获取Ingress资源的变化，动态地生成Load Balancer（例如Nginx）所需的配置文件（例如nginx.conf），然后重新加载Load Balancer（例如执行`nginx -s load`重新加载Nginx。）来生成新的路由转发规则。

![S2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4614073161/p241496.png)

Ingress Controller可通过配置LoadBalancer类型的Service来创建SLB，因此可以从外部通过SLB访问到Kubernetes集群的内部服务。根据Ingress配置的不同规则来访问不同的服务。

## ACK的Ingress

当前Kubernetes官方维护的是Nginx Ingress Controller，ACK基于社区版的Nginx Ingress Controller进行了优化。当在创建ACK集群时，您选择安装的Nginx Ingress Controller组件即为ACK定制版的Nginx Ingress Controller组件。

**相关文档**  


[Ingress高级用法](/cn.zh-CN/Kubernetes集群用户指南/网络管理/Ingress管理/Ingress高级用法.md)

[路由配置说明](/cn.zh-CN/Kubernetes集群用户指南/网络管理/Ingress管理/路由配置说明.md)

[Ingress访问日志分析与监控](/cn.zh-CN/Kubernetes集群用户指南/网络管理/Ingress管理/Ingress访问日志分析与监控.md)

[通过Ingress Controller来实现应用的流量复制](/cn.zh-CN/Kubernetes集群用户指南/网络管理/网络管理最佳实践/通过Ingress Controller来实现应用的流量复制.md)

