---
keyword: [kube-proxy-worker-windows组件介绍, kube-proxy-worker-windows使用说明, kube-proxy-worker-windows组件变更记录]
---

# kube-proxy-worker-windows

本文介绍kube-proxy-worker-windows的组件介绍、使用说明和组件变更记录。

## 组件介绍

kube-proxy-worker-windows是ACK Pro版集群上使用的容器化kube-proxy，用于管理Windows节点上Service的访问入口，包括集群内Pod到Service的访问和集群外访问Service。

## 使用说明

创建ACK Pro版集群时，kube-proxy-worker-windows默认会以DaemonSet工作负载的形式安装。kube-proxy-worker-windows依赖Windows工作节点上的特权托管进程，仅适用于集群内含有`window.alibabacloud.com/deployment-topology=2.0`标签的Windows工作节点。创建Windows节点时，会默认为节点打上`window.alibabacloud.com/deployment-topology=2.0`标签。

如果集群内没有Windows工作节点，且Windows工作节点没有`window.alibabacloud.com/deployment-topology=2.0`标签，则kube-proxy-worker-windows工作负载副本数始终为0。

**说明：** 如果您的Windows工作节点已包含`window.alibabacloud.com/deployment-topology=2.0`标签，却没有kube-proxy-worker-windows工作负载，请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)。

## 变更记录

**2021年04月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.20.4-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/kube-proxy-windows:v1.20.4-aliyun.1|2021年04月23日|-   具有`window.alibabacloud.com/deployment-topology=2.0`标签的Windows节点会默认安装wins，可以通过wins在节点上代理部署kube-proxy v1.20.4-aliyun.1。
-   支持Multiple Windows Server Release 1809（10.0.17763）、1909（10.0.18363.1440）和2004（10.0.19041.867）。

|无|
|v1.18.8-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/kube-proxy-windows:v1.18.8-aliyun.1|2021年04月23日|-   具有`window.alibabacloud.com/deployment-topology=2.0`标签的Windows节点会默认安装wins，可以通过wins在节点上代理部署kube-proxy v1.18.8-aliyun.1。
-   支持Multiple Windows Server Release 1809（10.0.17763）、1909（10.0.18363.1440）和2004（10.0.19041.867）。

|无|
|v1.16.9-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/kube-proxy-windows:v1.16.9-aliyun.1|2021年04月23日|-   具有`window.alibabacloud.com/deployment-topology=2.0`标签的Windows节点会默认安装wins，可以通过wins在节点上代理部署kube-proxy v1.16.9-aliyun.1。
-   支持Multiple Windows Server Release 1809（10.0.17763）、1909（10.0.18363.1440）和2004（10.0.19041.867）。

|无|

