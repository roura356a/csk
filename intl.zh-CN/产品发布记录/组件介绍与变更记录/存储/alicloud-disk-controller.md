---
keyword: [alicloud-disk-controller, alicloud-disk-controller组件变更记录]
---

# alicloud-disk-controller

alicloud-disk-controller支持自动创建云盘卷。本文介绍alicloud-disk-controller组件相关内容的最新动态。

## 组件介绍

在计算机中，磁盘控制器（Disk Controller）是一种电路，它允许中央处理器（CPU）与其他计算机磁盘（如软盘、硬盘或其他类型的磁盘驱动器）进行通信。磁盘控制器的基本操作包括读写操作的变化，不同类型的磁盘控制器可以作为计算机中的一个组件。alicloud-disk-controller组件支持云盘卷的自动创建。

## 使用说明

关于alicloud-disk-controller组件的安装，请参见[安装Disk Controller](/intl.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/安装插件.md)。

## 变更记录

**2021年04月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.16.9.55|registry.cn-hangzhou.aliyuncs.com/acs/alicloud-disk-controller:v1.16.9.55-c43698d4-aliyun|2021年04月05日|适配Kubernetes 1.20版本，删除metadata的selflink字段。|此次升级不会对业务造成影响。|

**2020年07月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.14.8.51|registry.cn-hangzhou.aliyuncs.com/acs/alicloud-disk-controller:v1.14.8.51-842f0a81-aliyun|2020年07月09日|-   支持Region单元化域名。
-   基础镜像Alpine版本升级到3.11.6。

|此次升级不会对业务造成影响。|

**2020年04月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.14.8.44|registry.cn-hangzhou.aliyuncs.com/acs/alicloud-disk-controller:v1.14.8.44-c23b62c5-aliyun|2020年04月14日|-   托管集群权限收敛。
-   Available类型StorageClass创建云盘规则修改：先创建SSD，再创建高效云盘。

|此次升级不会对业务造成影响。|

**2020年03月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.14.8.37|registry.cn-hangzhou.aliyuncs.com/acs/alicloud-disk-controller:v1.14.8.37-bd3fd891-aliyun|2020年03月06日|支持Region单元化域名。|此次升级不会对业务造成影响。|

**2019年08月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.12.6.21|registry.cn-hangzhou.aliyuncs.com/acs/alicloud-disk-controller:v1.12.6.21-54d91d6-aliyun|2019年08月06日|-   更新模板，支持Essd StorageClass。
-   关闭Leader Selector。
-   修复PVC状态为Lost时，PV重复创建的问题。
-   支持云盘Byok加密。

|此次升级不会对业务造成影响。|

**2019年02月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.11.2.5|registry.cn-hangzhou.aliyuncs.com/acs/alicloud-disk-controller:v1.11.2.5-1bea041-aliyun|2019年02月18日|添加StorageClass Topology特性，支持延迟绑定。|此次升级不会对业务造成影响。|

**2018年09月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.10.4|registry.cn-hangzhou.aliyuncs.com/acs/alicloud-disk-controller:v1.10.4-1847e0f-aliyun|2018年09月13日|StorageClass支持多Zone轮转。|此次升级不会对业务造成影响。|

**2018年07月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.10.4|registry.cn-hangzhou.aliyuncs.com/acs/alicloud-disk-controller:v1.10.4-821d9c2-aliyun|2018年07月24日|支持加密云盘。|此次升级不会对业务造成影响。|

