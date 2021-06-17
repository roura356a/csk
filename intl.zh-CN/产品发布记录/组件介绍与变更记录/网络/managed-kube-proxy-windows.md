---
keyword: [managed-kube-proxy-windows组件介绍, managed-kube-proxy-windows使用说明, managed-kube-proxy-windows组件变更记录]
---

# managed-kube-proxy-windows

本文介绍managed-kube-proxy-windows的组件信息、使用说明和变更记录。

## 组件介绍

managed-kube-proxy-windows是ACK Pro版集群上使用的容器化kube-proxy，用于管理Windows节点上Service的访问入口，包括集群内Pod到Service的访问和集群外访问Service。

## 使用说明

创建ACK Pro版集群时，managed-kube-proxy-windows默认会以DaemonSet工作负载的形式安装。managed-kube-proxy-windows依赖Windows工作节点上的特权托管进程，仅适用于从集群节点池创建节点时已含有`window.alibabacloud.com/deployment-topology=2.0`标签的Windows工作节点。创建Windows节点时，会默认为节点打上`window.alibabacloud.com/deployment-topology=2.0`标签。

如果您的集群内没有Windows工作节点，且Windows工作节点没有`window.alibabacloud.com/deployment-topology=2.0`标签，则managed-kube-proxy-windows工作负载副本数始终为0。

如果您的Windows工作节点已包含`window.alibabacloud.com/deployment-topology=2.0`标签，却没有managed-kube-proxy-windows工作负载。您需要手动安装managed-kube-proxy-windows 。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在集群管理页面选择**运维管理** \> **组件管理**。
3.  在**组件管理**页面找到managed-kube-proxy-windows ，单击managed-kube-proxy-windows右下方的**安装**。
4.  在弹出的对话框单击**确定**。

## 变更记录

**2021年06月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.20.4-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/kube-proxy-windows:v1.20.4-aliyun.1|2021年06月17日|-   更新basic镜像，同时覆盖原版本号。
-   支持 Multiple Windows Server Release 1809（10.0.17763.1935） 、1909（10.0.18363.1556）、 2004（10.0.19041.985）。

|无|
|v1.18.8-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/kube-proxy-windows:v1.18.8-aliyun.1|2021年06月17日|-   更新basic镜像，同时覆盖原版本号。
-   支持 Multiple Windows Server Release 1809（10.0.17763.1935）、1909（10.0.18363.1556）、2004（10.0.19041.985）。

|无|
|v1.16.9-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/kube-proxy-windows:v1.16.9-aliyun.1|2021年06月17日|-   更新basic镜像，同时覆盖原版本号。
-   支持 Multiple Windows Server Release 1809（10.0.17763.1935）、1909（10.0.18363.1556）、2004（10.0.19041.985）。

|无|

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

