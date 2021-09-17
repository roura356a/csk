---
keyword: [OSS存储卷, FAQ, 常见问题, Flexvolume]
---

# OSS存储卷FAQ

本文为您介绍OSS存储卷常见问题的处理方法。

-   [OSS存储卷挂载失败](#section_td0_7vk_92o)
-   [集群升级后容器内OSS挂载目录不可用](#section_n33_3bc_3jb)
-   [OSS存储卷挂载时间延长](#section_oj3_ee8_ze2)

## OSS存储卷挂载失败

问题现象：

OSS存储卷挂载失败。

问题原因：

-   使用的AccessKey不正确。
-   OSS存储卷挂载使用的URL无法访问网络。

解决方法：

-   使用正确的AccessKey。
-   确认OSS存储卷挂载使用的URL可访问网络。

## 集群升级后容器内OSS挂载目录不可用

问题现象：

集群升级后，容器内OSS挂载目录不可用。

问题原因：

升级集群重启kubelet时，由于容器网络会重启，导致OSSFS进程重启，主机与容器目录映射失效。

解决方法：

需要重启容器，或重建Pod。您可以通过配置健康检查实现容器或Pod的自动重启。

关于使用OSS存储的更多信息，请参见[OSS存储卷概述](/intl.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/OSS存储卷/OSS存储卷概述.md)。

## OSS存储卷挂载时间延长

问题现象：

OSS存储卷挂载时间延长。

问题原因：

若您在应用模板中配置了securityContext.fsgroup参数，kubelet在存储卷挂载完成后会执行`chmod`或`chown`操作，导致挂载时间延长。

解决方法：

-   若应用模板中配置了securityContext.fsgroup参数，请删除securityContext下的fsgroup参数。
-   若需要将挂载目录内文件变成期望的UID和mode，可以手动将Bucket挂载到一台ECS。关于ECS实例挂载OSS，请参见[通过云存储网关挂载OSS](/intl.zh-CN/控制台用户指南/上传、下载和管理文件/通过云存储网关挂载OSS.md)。再通过命令行执行`chown`和`chmod`，完成后通过Flexvolume使用OSS存储卷。关于如何通过Flexvolume使用OSS存储卷，请参见[使用OSS静态存储卷](/intl.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/OSS存储卷/使用OSS静态存储卷.md)。
-   对于1.20及之后版本的Kubernetes集群，可通过将fsGroupChangePolicy配置为OnRootMismatch，这时只有在首次启动时才会执行`chmod`或`chown`操作。更多信息，请参见[为Pod或容器配置安全性上下文](https://kubernetes.io/zh/docs/tasks/configure-pod-container/security-context/#%E4%B8%BA-pod-%E9%85%8D%E7%BD%AE%E5%8D%B7%E8%AE%BF%E9%97%AE%E6%9D%83%E9%99%90%E5%92%8C%E5%B1%9E%E4%B8%BB%E5%8F%98%E6%9B%B4%E7%AD%96%E7%95%A5)。

