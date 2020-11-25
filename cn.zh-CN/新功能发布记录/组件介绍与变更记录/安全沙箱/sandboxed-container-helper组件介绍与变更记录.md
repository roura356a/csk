---
keyword: [sandboxed-container-helper, 变更版本]
---

# sandboxed-container-helper组件介绍与变更记录

本文为您介绍sandboxed-container-helper组件的功能并展示该组件的变更记录。

## 组件介绍

sandboxed-container-helper是为安全沙箱提供诊断和运维的组件，该组件主要提供以下功能。

-   提供了针对安全沙箱DeviceMapper存储空间数据采集的Prometheus exporter，您可以在 ACK 集群中通过安装ack-arms-prometheus实现DeviceMapper存储空间指标的监控和告警，详细介绍请参见[托管阿里云Prometheus监控](/cn.zh-CN/Kubernetes集群用户指南/监控管理/托管阿里云Prometheus监控.md)。
-   检测安全沙箱节点上是否有泄露的存储、容器或者Orphan Pod，并且向 kube-apiserver 上报异常事件。您可以在 ACK 集群中安装ack-node-problem-detector来采集和监控对应的事件，详细介绍请参见[事件监控](/cn.zh-CN/Kubernetes集群用户指南/监控管理/事件监控.md) 。

## 变更记录

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.0-7a70086-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/sandboxed-container-helper:v1.0.0-7a70086-aliyun|2020年05月12日|增加新功能：-   上报容器泄漏、孤儿Pod的异常事件给kube-apiserver。
-   提供DeviceMapper空间使用状况的指标。
-   提供了修复异常事件的脚本。

|此次升级不会对业务造成影响。|

