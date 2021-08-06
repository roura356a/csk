---
keyword: [metrics-server, 使用说明, 变更记录]
---

# metrics-server

metrics-server是离线监控数据组件。即使您处于离线状态，您也可以通过metrics-server组件查看监控数据。本文介绍metrics-server组件的详细信息、使用说明和变更记录。

## 组件介绍

metrics-server是阿里云容器服务Kubernetes版基于社区开源监控组件进行改造和增强的离线监控数据组件，提供查看集群离线监控数据功能，提供HPA和基础资源监控的能力。您可以通过Metrics API获取到采集的监控数据。

关于社区开源监控组件详细介绍，请参见[社区开源监控组件](https://github.com/kubernetes-sigs/metrics-server)。

## 使用说明

metrics-server提供容器基础资源监控能力。具体操作，请参见[基础资源监控](/cn.zh-CN/Kubernetes集群用户指南/可观测性/监控管理/基础资源监控.md)。

## 变更记录

**2021年05月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.3.8.5-307cf45-aliyun|registry.\{REGION\}.aliyuncs.com/acs/metrics-server:v0.3.8.5-307cf45-aliyun|2021年05月|-   兼容1.20以下集群版本。
-   支持新版云监控容器基础资源。

|此次升级不会对业务造成影响。|

**2021年04月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.2.2-b4bf266-aliyun|registry.\{REGION\}.aliyuncs.com/acs/metrics-server:v0.2.2-b4bf266-aliyun|2021年04月|-   兼容支持Windows节点、Linux节点的指标采集。
-   支持调整多档位的弹性灵敏度，目前支持15s、20s、30s、60s四个档位。
-   修复应用滚动发布时HPA误弹的问题。

|建议升级组件到最新版本。|

**相关文档**  


[metrics-server组件常见问题]()

