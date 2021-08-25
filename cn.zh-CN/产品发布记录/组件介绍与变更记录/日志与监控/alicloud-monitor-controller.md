---
keyword: [alicloud-monitor-controller, 变更记录, 组件介绍, 使用说明]
---

# alicloud-monitor-controller

alicloud-monitor-controller是阿里云容器服务Kubernetes版提供对接云监控的系统组件。本文介绍alicloud-monitor-controller组件的信息、使用说明和变更记录。

## 组件介绍

alicloud-monitor-controller是阿里云容器服务Kubernetes版提供对接云监控的系统组件。当创建、变更、删除应用的时候，alicloud-monitor-controller会自动同步应用元数据到云监控，从而实现应用的容器监控。此外alicloud-monitor-controller还提供设置报警模板的功能，开发者可以通过控制台开启该功能。

## 使用说明

关于alicloud-monitor-controller组件的使用，请参见[容器服务报警管理](/cn.zh-CN/Kubernetes集群用户指南/可观测性/容器服务报警管理.md)。

## 变更记录

**2021年08月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.5.12-f7aedb4a-aliyun|registry.\{REGION\}.aliyuncs.com/acs/alicloud-monitor-controller:v1.5.12-f7aedb4a-aliyun|2021年08月23日|优化容器服务报警配置能力。|此次升级不会对业务造成影响。|
|v1.5.10-a75e4ee2-aliyun|registry.\{REGION\}.aliyuncs.com/acs/alicloud-monitor-controller:v1.5.10-a75e4ee2-aliyun|2021年08月19日|优化容器服务报警配置能力。|此次升级不会对业务造成影响。|

**2021年04月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.5.7-e1d5de8a-aliyun|registry.\{REGION\}.aliyuncs.com/acs/alicloud-monitor-controller:v1.5.7-e1d5de8a-aliyun|2021年04月23日|支持容器服务事件报警能力，可通过CRD方式配置容器服务事件报警。|此次升级不会对业务造成影响。|

**2020年07月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.4.0-49ff2362-aliyun|registry.\{REGION\}.aliyuncs.com/acs/alicloud-monitor-controller:v1.4.0-49ff2362-aliyun|2020年07月24日|-   支持采集Windows节点池与Linux节点池混合部署场景。
-   支持调整多档位的弹性灵敏度，目前支持15s、20s、30s、60s四个档位。
-   修复应用滚动更新时HPA误弹的问题。

|此次升级不会对业务造成影响。|

## 问题诊断

**云监控无应用分组**

请按照以下方式进行预检查：

-   检查kube-system命名空间下alicloud-monitor-controller的Pod是否正常运行。
-   检查组件版本，如果组件的版本与当前最新的版本存在差异，建议升级组件版本。
-   检查组件对应的日志，查看是否存在网络超时、SDK调用报错等问题。

按上述说明检查后，没有发现问题，请按照以下工单模板[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)。

工单模板

1.  是否已更新至最新版本。

    是

2.  组件日志是否存在SDK调用流控等异常问题，如存在异常请删除Pod。

    未发现，重启未恢复

3.  工单内附完整alicloud-monitor-controller日志。

    上传日志详情压缩包


**云监控应用分组无数据**

请按照以下方式进行预检查。

检查应用分组中实例名称是否与应用的Pod名称相对应：

-   如果实例名称异常，请按照上述云监控无应用分组的检查方法进行检查。
-   如果实例名称正常，则检查kube-system命名空间下的metrics-server的Pod是否正常运行，检查日志是否正常稳定输出。如果日志中出现`Successful write 164190 bytes metrics to monitor server`，则表明日志正常稳定输出。

按上述说明检查后，没有发现问题，请按照以下工单模板[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)。

工单模板

1.  检查分组中实例的名称是否与应用的Pod一致。

    是

2.  检查kube-system下的metrics-server组件是否可以正确输出日志。

    是

3.  提供集群ID，应用名称，Pod名称。

