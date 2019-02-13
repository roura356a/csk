# 如何选择Kubernetes集群网络插件：Terway和Flannel {#concept_qsd_ckm_q2b .concept}

本文详细介绍了容器服务在Kubernetes集群创建时提供的两种网络插件：Terway和Flannel，为您在创建集群选择网络插件时提供参考。

在创建Kubernetes集群时，阿里云容器服务提供两种网络插件：Terway和Flannel：

-   Flannel：使用的是简单稳定的社区的[Flannel](https://github.com/coreos/flannel) CNI插件，配合阿里云的VPC的高速网络，能给集群高性能和稳定的容器网络体验，但功能偏简单，支持的特性少，例如：不支持基于Kubernetes标准的Network Policy。
-   Terway：是阿里云容器服务自研的网络插件，功能上完全兼容Flannel，支持将阿里云的弹性网卡分配给容器，支持基于Kubernetes标准的NetworkPolicy来定义容器间的访问策略，支持对单个容器做带宽的限流。对于不需要使用Network Policy的用户，可以选择Flannel，其他情况建议选择Terway。了解更多Terway网络插件的相关内容，请参见[Terway网络插件](../../../../../intl.zh-CN/用户指南/Kubernetes集群/网络管理/Terway网络插件.md#)。

