---
keyword: [Storage-Operator组件, Storage-Operator组件变更记录]
---

# storage-operator

storage-operator组件用于管理存储组件的生命周期。本文主要为您介绍storage-operator组件的功能、使用说明及变更记录。

## 组件介绍

storage-operator组件用于管理存储组件的生命周期。storage-operator部署为Deployment，依赖自身镜像中的默认组件配置和通过ConfigMap自定义的组件配置，进行存储组件的部署与升级操作，进而降低容器研发与运维的复杂性。

-   默认组件配置：每个发布的storage-operator组件都有对应的默认配置。
-   通过ConfigMap自定义配置：通过ConfigMap自定义组件配置，可配置版本信息、是否部署组件等信息。

Storage-Operator会优先使用自定义的组件配置，只有没有指定具体配置时，才使用镜像默认配置。

![Storage-Operator](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/9986656161/p253634.png)

**说明：**

-   Storage-Operator镜像发布时，都会有默认存储组件配置文件。
-   Storage-Operator运行时，会挂载一个对存储组件配置的ConfigMap文件，且读取该配置。
-   Storage-Operator会整合自定义配置和默认配置，综合判断是否部署与升级组件。

## 使用说明

关于在应用目录中部署storage-operator组件的具体操作，请参见[使用storage-operator进行存储组件的部署与升级](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/容器存储运维/使用storage-operator进行存储组件的部署与升级.md)。

## 变更记录

**2021年08月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.18.8.55-e398ce5-aliyun|registry-vpc.$\{region\}.aliyuncs.com/acs/storage-operator:v1.18.8.55-e398ce5-aliyun|2021年08月16日|-   支持CNFS默认创建容量型NAS。
-   支持CNFS默认创建的StorageClass中参数archiveOnDelete为false，删除PV时默认支持删除PV对应的子目录。
-   修复storage-monitor的CPU占用过高问题。

|此次升级不会对业务造成影响。|

**2021年06月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.18.8.37-c63030b-aliyun|registry-vpc.$\{region\}.aliyuncs.com/acs/storage-operator:v1.18.8.37-c63030b-aliyun|2021年06月25日|-   支持自动扩容能力。
-   支持CNFS。

|此次升级不会对业务造成影响。|

**2021年03月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.18.8.28-18cca7b-aliyun|registry-vpc.$\{region\}.aliyuncs.com/acs/storage-operator:v1.18.8.28-18cca7b-aliyun|2021年03月25日|新功能：

-   支持批量快照。
-   支持定时快照。
-   支持集群监控。

|此次升级不会对业务造成影响。|

