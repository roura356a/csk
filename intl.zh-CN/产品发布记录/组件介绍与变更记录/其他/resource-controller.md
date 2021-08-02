---
keyword: [resource-controller, CPU拓扑感知调度]
---

# resource-controller

本文介绍resource-controller组件相关内容的最新动态。

## 组件介绍

resource-controller是实现动态控制Pod资源的关键组件，使用ACK Pro集群的CPU拓扑感知调度需要安装此组件。

## 使用说明

有关resource-controller组件的使用详情，请参见以下文档：

-   [管理组件](/intl.zh-CN/Kubernetes集群用户指南/组件/管理组件.md)
-   [CPU拓扑感知调度](/intl.zh-CN/Kubernetes集群用户指南/调度/CPU拓扑感知调度/CPU拓扑感知调度.md)

## 变更记录

**2021年06月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.2.2-0ac97de0-aliyun|-   registry.cn-hangzhou.aliyuncs.com/acs/node-resource-agent:v21.3.9.0-adecd8a-aliyun
-   registry.cn-hangzhou.aliyuncs.com/acs/resource-controller:v1.2.2-0ac97de0-aliyun

|2021年06月21日|增加MBA控制、修复Change Memory和Containerd、升级client-go 。|无|

**2021年05月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.2.1-d1e280f-aliyun|-   registry.cn-hangzhou.aliyuncs.com/acs/node-resource-agent:v21.3.9.0-adecd8a-aliyun
-   registry.cn-hangzhou.aliyuncs.com/acs/resource-controller:v1.2.1-d1e280f-aliyun

|2021年05月21日|增加L3控制、适配AMD拓扑感知调度。|无|

**2021年04月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.2.0-ec8a979-aliyun|-   registry.cn-hangzhou.aliyuncs.com/acs/node-resource-agent:v21.3.9.0-adecd8a-aliyun
-   registry.cn-hangzhou.aliyuncs.com/acs/resource-controller:v1.2.0-ec8a979-aliyun

|2021年04月20日|适配containerd、多container、meta-data访问、container粒度日志、增加实时动态水位控制等。|无|

**2021年03月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.1.0-e7388cb|-   registry.cn-hangzhou.aliyuncs.com/acs/node-resource-agent:v21.3.9.0-adecd8a-aliyun
-   registry.cn-hangzhou.aliyuncs.com/acs/resource-controller:v1.1.0-e7388cb

|2021年03月09日|发布node-agent，为记录numainfo的configmap添加`label`字段。|无|

**2021年01月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.1.0-e7388cb|-   registry.cn-hangzhou.aliyuncs.com/acs/node-resource-agent:121ffbe
-   registry.cn-hangzhou.aliyuncs.com/acs/resource-controller:v1.1.0-e7388cb

|2021年01月26日|resource-controller支持分布式部署分发、cpuset参数调节等。|无|

