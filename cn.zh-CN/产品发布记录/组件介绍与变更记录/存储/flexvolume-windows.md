---
keyword: flexvolume-windows
---

# flexvolume-windows

flexvolume-windows是ACK集群上使用的容器存储插件。本文介绍flexvolume-windows组件信息、使用说明和变更记录。

## 组件介绍

flexvolume-windows是ACK集群上使用的容器存储插件，用于在Windows容器挂载使用阿里云云盘（块存储）和阿里云文件存储NAS服务。

## 使用说明

flexvolume-windows支持v1.16.9及其以上版本的ACK集群，需要创建集群时指定使用FlexVolume作为存储插件。

**说明：** 创建集群时默认FlexVolume不可选，您需要[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)申请白名单。

flexvolume-windows目前仍需要以DaemonSet工作负载的形式手动安装。具体操作，请参见[在Windows容器中使用云盘及基于SMB的文件存储](/cn.zh-CN/Kubernetes集群用户指南/Windows容器/在Windows容器中使用云盘及基于SMB的文件存储.md)的步骤二：为Windows节点安装Flexvolume插件。

## 变更记录

**2021年08月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.0|registry-vpc.$\{region\}.aliyuncs.com/acs/flexvolume-windows:v1.0.0|2021年08月23日|支持 Multiple Windows Server Release 1809（10.0.17763.2114）、1909（10.0.18363.1556）、2004（10.0.19041.1165）。|此次升级不会对业务造成影响。|

