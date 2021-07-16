---
keyword: [ack-kubernetes-cronhpa-controller组件介绍, ack-kubernetes-cronhpa-controller使用说明, ack-kubernetes-cronhpa-controller变更记录]
---

# ack-kubernetes-cronhpa-controller

ack-kubernetes-cronhpa-controller组件用于实现资源定时扩容。本文介绍ack-kubernetes-cronhpa-controller组件信息、使用说明和变更记录。

## 组件介绍

ack-kubernetes-cronhpa-controller是一个Kubernetes HPA Controller，您可以使用ack-kubernetes-cronhpa-controller组件实现容器定时伸缩。定时伸缩CronHorizontalPodAutoscaler（简称CronHPA）通过设置定时的方式触发容器的水平副本伸缩。您可以把CronHPA用在任何Kubernetes中定义的对象上，只要该对象支持伸缩子资源的项目已在GitHub上开源，例如Deployment和StatefulSet。

## 使用说明

关于ack-kubernetes-cronhpa-controller组件的使用说明，请参见[容器定时伸缩（CronHPA）](/intl.zh-CN/Kubernetes集群用户指南/弹性伸缩/容器定时伸缩（CronHPA）.md)。

## 变更记录

**2021年05月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.4.0-a2f4954d-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/kubernetes-cronhpa-controller:v1.4.0-a2f4954d-aliyun|2021年05月27日|修复elasticworkloads授权问题。|此次升级不会对业务造成影响。|

**2020年12月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.4.0-fc4f6060-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/kubernetes-cronhpa-controller:v1.4.0-fc4f6060-aliyun|2020年12月18日|-   支持CronHPA基础功能。
-   支持与HPA兼容。

|此次升级不会对业务造成影响。|

