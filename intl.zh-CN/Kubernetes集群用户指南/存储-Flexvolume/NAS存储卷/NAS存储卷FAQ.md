---
keyword: [NAS存储卷, FAQ, 常见问题, Flexvolume]
---

# NAS存储卷FAQ

本文为您介绍NAS存储卷常见问题的处理方法。

-   [NAS存储卷挂载时间太长](#section_td0_7vk_92o)
-   [NAS存储卷挂载失败时出现timeout错误](#section_n33_3bc_3jb)
-   [使用NAS存储卷时，提示chown: option not permitted](#section_vxc_7oy_i24)
-   [挂载NAS存储卷失败](#section_f1w_yob_goz)
-   [使用NAS动态存储卷时Controller的任务队列已满且无法创建新的PV](#section_xgz_7fw_p7e)

## NAS存储卷挂载时间太长

问题现象：

NAS存储卷挂载时间太长。

问题原因：

如果NAS存储卷包含的文件量很大，且在挂载模板中配置了`chmod`参数，可能导致挂载时间过长的问题。

解决方法：

您可以去掉`chmod`参数。

## NAS存储卷挂载失败时出现timeout错误

问题现象：

NAS存储卷挂载失败时出现timeout错误。

问题原因：

NAS挂载点和集群不在同一VPC内。

解决方法：

挂载NAS存储卷时，NAS挂载点和集群需要在同一VPC内。

## 使用NAS存储卷时，提示chown: option not permitted

问题现象：

使用NAS存储卷时，提示chown: option not permitted。

问题原因：

您的容器没有权限使用该NAS存储卷。

解决方法：

您需要使用Root权限启动容器。

## 挂载NAS存储卷失败

问题现象：

挂载NAS PV失败，并报以下错误：

```
Unable to mount volumes for pod "dp-earnings-pod_default(906172c6-3d68-11e8-86e0-00163e00****)": timeout expired waiting for volumes to attach/mount for pod "default"/"dp-earnings-pod". list of unattached/unmounted volumes=[vol1 vol2]
```

问题原因：

您没有安装Flexvolume插件。

解决方法：

您需要安装Flexvolume插件。具体操作，请参见[安装与升级Flexvolume组件](/intl.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/安装与升级Flexvolume组件.md)。

## 使用NAS动态存储卷时Controller的任务队列已满且无法创建新的PV

问题现象：

使用NAS动态存储卷时，若创建子目录写入速度快于Controller删除子目录速度，将可能导致Controller的任务队列阻塞且无法创建新的PV。

问题原因：

当集群使用动态NAS存储卷时，配置的StorageClass回收策略reclaimPolicy为Delete且archiveOnDelete为false。

解决方案：

将archiveOnDelete配置为true，当删除PV时只是修改NAS文件系统中子目录的名称，而不是真正删除文件。

具体的文件删除操作需要您自行处理，例如：在某个节点过载根目录启动定时删除机制，或启动多个Pod并发删除某种格式的子目录。

