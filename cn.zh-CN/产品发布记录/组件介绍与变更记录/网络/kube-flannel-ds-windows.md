---
keyword: [kube-flannel-ds-windows组件介绍, kube-flannel-ds-windows使用说明, kube-flannel-ds-windows组件变更记录]
---

# kube-flannel-ds-windows

本文介绍kube-flannel-ds-windows的组件介绍、使用说明和组件变更记录。

## 组件介绍

kube-flannel-ds-windows是ACK Pro版集群上使用的容器网络插件，用于构建适合Windows容器通讯的L2Bridge集群网络。

## 使用说明

当使用Flannel作为ACK Pro版集群的网络插件时，kube-flannel-ds-windows默认会以DaemonSet工作负载的形式安装。kube-flannel-ds-windows依赖Windows工作节点上的特权托管进程，仅适用于集群内含有`window.alibabacloud.com/deployment-topology=2.0`标签的Windows工作节点。创建Windows节点时，会默认为节点打上`window.alibabacloud.com/deployment-topology=2.0`标签。

如果集群内没有Windows工作节点，且Windows工作节点没有`window.alibabacloud.com/deployment-topology=2.0`标签，则kube-flannel-ds-windows工作负载副本数始终为0。

**说明：** 如果您的Windows工作节点已包含`window.alibabacloud.com/deployment-topology=2.0`标签，却没有kube-flannel-ds-windows工作负载，请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)。

## 变更记录

**2021年05月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.13.1-aliyun.2|registry-vpc.$\{region\}.aliyuncs.com/acs/flannel-windows:v0.13.1-aliyun.2|2021年05月13日|-   支持通过ContainerD部署时自动切换CNI配置。
-   支持Multiple Windows Server Release 1809（10.0.17763）、1909（10.0.18363.1440）和2004（10.0.19041.867）。

|无|

**2021年04月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.13.1-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/flannel-windows:v0.13.1-aliyun.1|2021年04月22日|-   具有`window.alibabacloud.com/deployment-topology=2.0`标签的Windows节点会默认安装wins，可以通过wins在节点上代理部署Flannel。
-   适用于L2Bridge网络和Overlay网络，在阿里云场景下，默认使用L2Bridge网络。
-   支持Multiple Windows Server Release 1809（10.0.17763）、1909（10.0.18363.1440）和2004（10.0.19041.867）。

|无|

