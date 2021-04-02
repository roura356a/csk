---
keyword: [csi-local-plugin, 本地存储CSI插件]
---

# csi-local-plugin

csi-local-plugin是边缘集群上使用的本地存储CSI插件，本文为您介绍csi-local-plugin相关内容的最新动态。

## 组件介绍

csi-local-plugin是Kubernetes的CSI插件接口的实现，用于本地LVM数据卷的创建、挂载、删除、卸载等，便于您通过原生的PV和PVC方式使用本地存储。

## 使用说明

csi-local-plugin是CSI标准插件，您只需通过Kubernetes的API创建PVC，并在Pod中挂载PVC即可使用。

## 变更记录

**2021年03月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.16.9-a9145c7-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.16.9-a9145c7-aliyun|2021年03月24日|首次发布csi-local-plugin组件。|无|

