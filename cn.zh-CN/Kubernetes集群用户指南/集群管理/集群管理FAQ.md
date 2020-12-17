# 集群管理FAQ

本文主要为您介绍集群管理的一般问题。

关于故障处理类问题，参见[常见问题](https://help.aliyun.com/knowledge_detail/148453.html)。

## 已有集群是否可以添加支持Intel SGX的节点？

集群满足以下三个条件后可以添加支持Intel SGX的节点：

-   Kubernetes版本大于等于1.14.0。
-   集群的网络插件为**Flannel**。
-   创建集群时操作系统为AliyunLinux 2.xxxx，并且在节点接入时不要选择自定义镜像。

您可以[容器服务管理控制台](https://cs.console.aliyun.com/)，找到符合以上三个条件的集群，并在该集群右侧的**操作**列选择**更多** \> **系统组件升级**，安装aesm和sgx-device-plugin。

## Alibaba Cloud Linux2操作系统的集群兼容CentOS的容器镜像吗？

暂不支持Centos 6的容器镜像，其他版本完全兼容。更多信息，请参见[使用容器优化操作系统Alibaba Cloud Linux 2](/cn.zh-CN/Kubernetes集群用户指南/集群管理/集群管理最佳实践/使用容器优化操作系统Alibaba Cloud Linux 2.md)。

