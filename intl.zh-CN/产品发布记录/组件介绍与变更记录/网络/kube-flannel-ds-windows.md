---
keyword: kube-flannel-ds-windows
---

# kube-flannel-ds-windows

本文介绍kube-flannel-ds-windows的组件介绍、使用说明和变更记录。

## 组件介绍

kube-flannel-ds-windows是ACK托管版集群上使用的容器网络插件，用于构建适合Windows容器通讯的L2Bridge集群网络。

## 使用说明

当使用Flannel作为ACK托管版集群的网络插件时，kube-flannel-ds-windows默认会以DaemonSet工作负载的形式安装。kube-flannel-ds-windows依赖Windows工作节点上的特权托管进程，仅适用于从集群节点池创建节点时已含有`windows.alibabacloud.com/deployment-topology=2.0`标签的Windows工作节点。创建Windows节点时，会默认为节点打上`windows.alibabacloud.com/deployment-topology=2.0`标签。

如果集群内没有Windows工作节点，且Windows工作节点没有`windows.alibabacloud.com/deployment-topology=2.0`标签，则kube-flannel-ds-windows工作负载副本数始终为0。

如果您的Windows工作节点已包含`windows.alibabacloud.com/deployment-topology=2.0`标签，却没有kube-flannel-ds-windows工作负载，您需要重新创建kube-flannel-cfg-windows和kube-flannel-ds-windows。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在控制台左侧导航栏中，单击**集群**。
3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。
4.  在集群管理页面选择**运维管理** \> **组件管理**。
5.  在**组件管理**页面找到kube-flannel-ds，单击![图标](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/3687983261/p283826.png)图标，然后单击**查看YAML**。
6.  在**查看YAML**面板复制kube-flannel-cfg-windows和kube-flannel-ds-windows内容。
7.  在集群管理页面选择**工作负载** \> **无状态**。
8.  在**无状态**页面单击**使用YAML创建资源**。
9.  设置**示例模板**为**自定义**，在**模板**中输入复制的kube-flannel-cfg-windows和kube-flannel-ds-windows内容，然后单击**创建**。

## 变更记录

**2021年08月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.13.1-aliyun.3|registry-vpc.$\{region\}.aliyuncs.com/acs/flannel-windows:v0.13.1-aliyun.3|2021年08月22日|-   更新basic镜像，同时覆盖原版本号。
-   支持Multiple Windows Server Release 1809（10.0.17763.2114）、1909（10.0.18363.1556）、2004（10.0.19041.1165）。

|此次升级不会对业务造成影响。|

**2021年07月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.13.1-aliyun.3|registry-vpc.$\{region\}.aliyuncs.com/acs/flannel-windows:v0.13.1-aliyun.3|2021年07月05日|-   更新basic镜像，同时覆盖原版本号。
-   支持Multiple Windows Server Release 1809（10.0.17763.1999）、1909（10.0.18363.1556）、2004（10.0.19041.1052）。

|此次升级不会对业务造成影响。|

**2021年06月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.13.1-aliyun.3|registry-vpc.$\{region\}.aliyuncs.com/acs/flannel-windows:v0.13.1-aliyun.3|2021年06月17日|-   更新basic镜像。
-   支持Multiple Windows Server Release 1809（10.0.17763.1935）、1909（10.0.18363.1556）、2004（10.0.19041.985）。

|此次升级不会对业务造成影响。|

**2021年05月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.13.1-aliyun.2|registry-vpc.$\{region\}.aliyuncs.com/acs/flannel-windows:v0.13.1-aliyun.2|2021年05月13日|-   支持通过ContainerD部署时自动切换CNI配置。
-   支持Multiple Windows Server Release 1809（10.0.17763.1879）、1909（10.0.18363.1500）、2004（10.0.19041.928）。

|此次升级不会对业务造成影响。|

**2021年04月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.13.1-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/flannel-windows:v0.13.1-aliyun.1|2021年04月22日|-   具有`windows.alibabacloud.com/deployment-topology=2.0`标签的Windows节点会默认安装wins，可以通过wins在节点上代理部署Flannel。
-   适用于L2Bridge网络和Overlay网络，在阿里云场景下，默认使用L2Bridge网络。
-   支持Multiple Windows Server Release 1809（10.0.17763.1879）、1909（（10.0.18363.1500）和2004（10.0.19041.928）。

|此次升级不会对业务造成影响。|

