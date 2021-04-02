---
keyword: [resource-controller, CPU拓扑感知调度]
---

# resource-controller

本文介绍resource-controller组件相关内容的最新动态。

## 组件介绍

resource-controller是实现动态控制Pod资源的关键组件，使用ACK Pro集群的CPU拓扑感知调度需要安装此组件。

## 使用说明

有关resource-controller组件的使用详情，请参见[CPU拓扑感知调度](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/资源调度/CPU拓扑感知调度.md)。

## 变更记录

**2021年03月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.1.0-e7388cb|-   registry.cn-hangzhou.aliyuncs.com/acs/node-resource-agent:v21.3.9.0-adecd8a-aliyun
-   registry.cn-hangzhou.aliyuncs.com/acs/resource-controller:v1.1.0-e7388cb

|2021年03月09号|发布node-agent，为记录numainfo的configmap添加`label`字段。|无|

**2021年01月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.1.0-e7388cb|-   registry.cn-hangzhou.aliyuncs.com/acs/node-resource-agent:121ffbe
-   registry.cn-hangzhou.aliyuncs.com/acs/resource-controller:v1.1.0-e7388cb

|2021年01月26号|resource-controller支持分布式部署分发、cpuset参数调节等。|无|

