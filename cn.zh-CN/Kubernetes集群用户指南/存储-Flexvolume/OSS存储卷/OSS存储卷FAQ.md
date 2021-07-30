---
keyword: [OSS存储卷, FAQ, 常见问题, Flexvolume]
---

# OSS存储卷FAQ

本文为您介绍OSS存储卷常见问题的处理方法。

-   [OSS存储卷挂载失败](#section_td0_7vk_92o)
-   [集群升级后容器内OSS挂载目录不可用](#section_n33_3bc_3jb)

## OSS存储卷挂载失败

问题现象：

OSS存储卷挂载失败。

问题原因：

可能原因如下：

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

关于使用OSS存储的更多信息，请参见[OSS存储卷使用说明](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/OSS存储卷/OSS存储卷使用说明.md)。

