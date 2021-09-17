---
keyword: [OSS存储卷, FAQ, 常见问题, CSI]
---

# OSS存储卷FAQ

本文为您介绍OSS存储卷常见问题的处理方法。

## OSS存储卷挂载时间延长

问题现象：

OSS存储卷挂载时间延长。

问题原因：

同时满足以下配置，kubelet在存储卷挂载过程中将执行chmod或chown操作，导致挂载时间延长。

-   在PV及PVC模板中配置的参数AccessModes值为ReadWriteOnce。
-   在应用模板中配置了securityContext.fsgroup参数。

解决方法：

-   若应用模板中配置了securityContext.fsgroup参数，请删除securityContext下的fsgroup参数。
-   若需要将挂载目录内文件变成期望的UID和mode，可以手动将Bucket挂载到一台ECS。关于如何将OSS挂载到ECS实例，请参见[通过云存储网关挂载OSS](/cn.zh-CN/控制台用户指南/文件管理/通过云存储网关挂载OSS.md)。再通过命令行执行`chown`和`chmod`，完成后通过CSI使用OSS存储卷。关于如何通过CSI使用OSS存储卷，请参见[使用OSS静态存储卷](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/OSS存储卷/使用OSS静态存储卷.md)。
-   对于1.20及之后版本的Kubernetes集群，除了上述两种解决方法外，也可通过将fsGroupChangePolicy配置为OnRootMismatch，这时只有在首次启动时才会执行`chmod`或`chown`操作，导致存在挂载时间延长的问题，后续挂载OSS存储卷时挂载时间将恢复正常。关于fsGroupChangePolicy参数的更多信息，请参见[为Pod或容器配置安全性上下文](https://kubernetes.io/zh/docs/tasks/configure-pod-container/security-context/#%E4%B8%BA-pod-%E9%85%8D%E7%BD%AE%E5%8D%B7%E8%AE%BF%E9%97%AE%E6%9D%83%E9%99%90%E5%92%8C%E5%B1%9E%E4%B8%BB%E5%8F%98%E6%9B%B4%E7%AD%96%E7%95%A5)。

