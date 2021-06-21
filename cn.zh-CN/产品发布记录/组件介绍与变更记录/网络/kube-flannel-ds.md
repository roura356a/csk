---
keyword: [kube-flannel-ds, 容器overlay网络插件]
---

# kube-flannel-ds

kube-flannel-ds是在阿里云边缘容器服务ACK@Edge上使用的Overlay容器网络组件。本文为您介绍该组件，以及组件相关内容的最新动态。

## 组件介绍

创建边缘集群时会默认安装kube-flannel-ds网络组件，该组件通过DaemonSet方式部署，且通过在集群节点上配置VXLAN设备、FDB表及路由信息，来保证容器在相同节点或不同节点之间的互通。

## 使用说明

组件已自动配置CNI，您只需通过Kubernetes的API创建应用即可使用kube-flannel-ds。

## 变更记录

**2021年05月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.11.0.6-1283a29-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/flannel:v0.11.0.6-1283a29-aliyun|2021年05月|新增启动参数：enable-cloud-edge-isolate，支持配置边缘节点与云端节点之间互通Overlay路由。|此次升级不会对业务造成影响。|

**2021年01月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.11.0.5-437a359-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/flannel:v0.11.0.5-437a359-aliyun|2021年01月|无。|此次升级不会对业务造成影响。|

