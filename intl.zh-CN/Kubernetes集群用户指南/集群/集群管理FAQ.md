# 集群管理FAQ

本文主要为您介绍集群管理的一般问题。

-   [已有集群是否可以添加支持Intel SGX的节点？](#section_f5n_8ir_imk)
-   [Alibaba Cloud Linux2操作系统的集群兼容CentOS的容器镜像吗？](#section_g8z_g18_h2h)
-   [为何创建Kubernetes集群失败？](~~86762~~)
-   [Kubernetes集群扩容常见问题的排查及解决方法](~~178936~~)
-   [为何删除Kubernetes集群失败？](~~86763~~)
-   [通过CloudShell管理集群出现超时问题](~~141857~~)
-   [创建集群后，是否可以更改容器运行时？](#section_4hy_0ag_uhv)

## 已有集群是否可以添加支持Intel SGX的节点？

集群满足以下三个条件后可以添加支持Intel SGX的节点：

-   Kubernetes版本大于等于1.14.0。
-   集群的网络插件为**Flannel**。
-   创建集群时操作系统为AliyunLinux 2.xxxx，并且在节点接入时不要选择自定义镜像。

您可以[容器服务管理控制台](https://cs.console.aliyun.com/)，找到符合以上三个条件的集群，并在该集群右侧的**操作**列选择**更多** \> **系统组件升级**，安装aesm和sgx-device-plugin。

## Alibaba Cloud Linux2操作系统的集群兼容CentOS的容器镜像吗？

完全兼容。更多信息，请参见[使用操作系统Alibaba Cloud Linux 2](/intl.zh-CN/Kubernetes集群用户指南/集群/操作系统/使用操作系统Alibaba Cloud Linux 2.md)。

## 创建集群后，是否可以更改容器运行时？

问题现象：创建集群的时候，选择的容器运行时为Containerd，如何改回Docker运行时？

解决办法：创建集群后，不支持切换容器运行时，但是您可以创建不同类型运行时的节点池。节点池与节点池的运行时可以不同。更多信息，请参见[节点池概述](/intl.zh-CN/Kubernetes集群用户指南/节点与节点池/节点池/节点池概述.md)。

