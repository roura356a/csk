---
keyword: [NAS存储卷, FAQ, 常见问题, Flexvolume]
---

# NAS存储卷FAQ

本文为您介绍NAS存储卷常见问题的处理方法。

-   [NAS存储卷挂载时间延长](#section_td0_7vk_92o)
-   [NAS存储卷挂载失败时出现timeout错误](#section_n33_3bc_3jb)
-   [使用NAS存储卷时，提示chown: option not permitted](#section_vxc_7oy_i24)
-   [挂载NAS存储卷失败](#section_f1w_yob_goz)
-   [使用NAS动态存储卷时Controller的任务队列已满且无法创建新的PV](#section_xgz_7fw_p7e)

## NAS存储卷挂载时间延长

问题现象：

NAS存储卷挂载时间延长。

问题原因：

若您在应用模板中配置了securityContext.fsgroup参数，kubelet在存储卷挂载完成后会执行`chmod`或`chown`操作，导致挂载时间延长。

解决方法：

-   若应用模板中配置了securityContext.fsgroup参数，请删除securityContext下的fsgroup参数。。
-   若需要将挂载目录内文件变成期望的UID和mode，可以手动将目标目录挂载到一台ECS。关于ECS实例挂载文件系统，请参见[通过控制台实现ECS实例一键挂载文件系统]()。再通过命令行执行`chown`和`chmod`，完成后通过Flexvolume使用NAS存储卷。关于如何通过Flexvolume使用NAS存储卷，请参见[使用NAS静态存储卷](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/NAS存储卷/使用NAS静态存储卷.md)和[使用NAS动态存储卷](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/NAS存储卷/使用NAS动态存储卷.md)。
-   对于1.20及之后版本的Kubernetes集群，可通过将fsGroupChangePolicy配置为OnRootMismatch，这时只有在首次启动时才会执行`chmod`或`chown`操作。更多信息，请参见[为Pod或容器配置安全性上下文](https://kubernetes.io/zh/docs/tasks/configure-pod-container/security-context/#%E4%B8%BA-pod-%E9%85%8D%E7%BD%AE%E5%8D%B7%E8%AE%BF%E9%97%AE%E6%9D%83%E9%99%90%E5%92%8C%E5%B1%9E%E4%B8%BB%E5%8F%98%E6%9B%B4%E7%AD%96%E7%95%A5)。

## NAS存储卷挂载失败时出现timeout错误

问题现象：

NAS存储卷挂载失败时出现timeout错误。

问题原因：

NAS挂载点和集群不在同一VPC内。

解决方法：

选择与集群在同一VPC内的NAS挂载点。

## 使用NAS存储卷时，提示chown: option not permitted

问题现象：

使用NAS存储卷时，提示chown: option not permitted。

问题原因：

您的容器没有权限使用该NAS存储卷。

解决方法：

您需要使用root权限启动容器。

## 挂载NAS存储卷失败

问题现象：

挂载NAS PV失败，并报以下错误：

```
Unable to mount volumes for pod "dp-earnings-pod_default(906172c6-3d68-11e8-86e0-00163e00****)": timeout expired waiting for volumes to attach/mount for pod "default"/"dp-earnings-pod". list of unattached/unmounted volumes=[vol1 vol2]
```

问题原因：

您没有安装Flexvolume插件。

解决方法：

您需要安装Flexvolume插件。具体操作，请参见[安装与升级Flexvolume组件](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/安装与升级Flexvolume组件.md)。

## 使用NAS动态存储卷时Controller的任务队列已满且无法创建新的PV

问题现象：

使用NAS动态存储卷时，若创建子目录写入速度快于Controller删除子目录速度，将可能导致Controller的任务队列阻塞且无法创建新的PV。

问题原因：

当集群使用动态NAS存储卷时，配置的StorageClass回收策略reclaimPolicy为Delete且archiveOnDelete为false。

解决方法：

将archiveOnDelete配置为true，当删除PV时只是修改NAS文件系统中子目录的名称，而不是真正删除文件。

具体的文件删除操作需要您自行处理，例如：在某个节点过载根目录启动定时删除机制，或启动多个Pod并发删除某种格式的子目录。

