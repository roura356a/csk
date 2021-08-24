---
keyword: [NAS存储卷, FAQ, 常见问题]
---

# NAS存储卷FAQ

本文为您介绍NAS存储卷常见问题的处理方法。

-   [使用NAS时，提示chown: option not permitted](#section_srw_s3w_230)
-   [使用NAS动态存储卷时Controller的任务队列已满且无法创建新的PV](#section_gy3_aek_sdu)
-   [NAS存储卷挂载时间过长](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/NAS存储卷/NAS存储卷FAQ.md)

## 使用NAS时，提示chown: option not permitted

问题现象：

使用NAS时，提示chown: option not permitted。

问题原因：

您的容器没有权限使用该NAS。

解决方法：

使用Root权限启动容器。

## 使用NAS动态存储卷时Controller的任务队列已满且无法创建新的PV

问题现象：

使用NAS动态存储卷时，若创建子目录写入速度快于Controller删除子目录速度，将可能导致Controller的任务队列阻塞且无法创建新的PV。

问题原因：

当集群使用动态NAS存储卷时，配置的StorageClass回收策略reclaimPolicy为Delete且archiveOnDelete为false。

解决方法：

将archiveOnDelete配置为true，当删除PV时只是修改NAS文件系统中子目录的名称，而不是真正删除文件。

具体的文件删除操作需要您自行处理，例如：在某个节点过载根目录启动定时删除机制，或启动多个Pod并发删除某种格式的子目录。

## NAS存储卷挂载时间过长

问题现象：

NAS存储卷挂载时间过长。

问题原因：

-   若您在PV模板中配置了mode参数且挂载目录中有大量文件时，会导致执行挂载非常慢，甚至挂载失败。
-   若您在应用模板中配置了securityContext.fsgroup参数，kubelet在存储卷挂载完成后会执行`chmod`或`chown`操作，导致挂载时间过长。

解决方法：

-   若PV中配置了mode参数，请删除该参数。
-   若应用模板中配置了securityContext.fsgroup参数，请删除securityContext下的fsgroup参数。

