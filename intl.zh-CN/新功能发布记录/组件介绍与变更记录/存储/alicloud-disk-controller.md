---
keyword: [alicloud-disk-controller, alicloud-disk-controller组件变更记录]
---

# alicloud-disk-controller

alicloud-disk-controller支持自动创建云盘卷。本文介绍alicloud-disk-controller组件相关内容的最新动态。

## 2020年07月

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v1.14.8.51|registry.cn-hangzhou.aliyuncs.com/acs/alicloud-disk-controller:v1.14.8.51-842f0a81-aliyun|2020年07月09日|-   支持Region单元化域名。
-   基础镜像Alpine版本升级到3.11.6。 |

## 2020年04月

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v1.14.8.44|registry.cn-hangzhou.aliyuncs.com/acs/alicloud-disk-controller:v1.14.8.44-c23b62c5-aliyun|2020年04月14日|-   托管集群权限收敛。
-   Available类型Storageclass创建云盘规则修改：先创建SSD，再创建高效云盘。 |

## 2020年03月

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v1.14.8.37|registry.cn-hangzhou.aliyuncs.com/acs/alicloud-disk-controller:v1.14.8.37-bd3fd891-aliyun|2020年03月06日|支持Region单元化域名。|

## 2019年08月

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v1.12.6.21|registry.cn-hangzhou.aliyuncs.com/acs/alicloud-disk-controller:v1.12.6.21-54d91d6-aliyun|2019年08月06日|-   更新模板，支持Essd StorageClass。
-   关闭Leader Selector。
-   修复PVC状态为Lost时，PV重复创建的问题。
-   支持云盘Byok加密。 |

## 2019年02月

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v1.11.2.5|registry.cn-hangzhou.aliyuncs.com/acs/alicloud-disk-controller:v1.11.2.5-1bea041-aliyun|2019年02月18日|添加Storageclass Topology特性，支持延迟绑定。|

## 2018年09月

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v1.10.4|registry.cn-hangzhou.aliyuncs.com/acs/alicloud-disk-controller:v1.10.4-1847e0f-aliyun|2018年09月13日|StorageClass支持多Zone轮转。|

## 2018年07月

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v1.10.4|registry.cn-hangzhou.aliyuncs.com/acs/alicloud-disk-controller:v1.10.4-821d9c2-aliyun|2018年07月24日|支持加密云盘。|

