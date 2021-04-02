---
keyword: [kube-flannel-ds, 容器overlay网络插件]
---

# kube-flannel-ds

kube-flannel-ds是边缘集群上使用的容器Overlay网络插件，本文为您介绍kube-flannel-ds相关内容的最新动态。

## 组件介绍

kube-flannel-ds是边缘集群默认的网络插件，通过Daemonset方式部署。通过在集群节点上配置VXLAN设备、FDB表及路由信息，保证容器在相同节点类型之间互通。

## 使用说明

您无须感知kube-flannel-ds的工作过程，只需通过Kubernetes的API创建应用即可使用kube-flannel-ds。

## 变更记录

**2021年1月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.11.0.5-437a359-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/flannel:v0.11.0.5-437a359-aliyun|2021年01月|不同节点类型之间容器不互通，例如边缘和云端节点。|无|

