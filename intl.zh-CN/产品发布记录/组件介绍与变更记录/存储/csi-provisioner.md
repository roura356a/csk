---
keyword: [csi-provisioner, csi-provisioner组件变更记录]
---

# csi-provisioner

csi-provisioner支持数据卷的自动创建能力。本文介绍csi-provisioner组件相关内容的最新动态。

## 组件介绍

阿里云CSI-Provisioner组件提供了数据卷的自动创建能力，目前仅支持云盘、NAS两种存储卷，要求容器服务Kubernetes为1.14及以上版本。

## 使用说明

更多信息，请参见[存储CSI](/intl.zh-CN/Kubernetes集群用户指南/存储-CSI/存储CSI概述.md)。

## 变更记录

**2021年04月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.1.2|registry.cn-hangzhou.aliyuncs.com/acs/csi-provisioner:v1.6.0-e360c7e43-aliyun|2021年04月08日|-   适配Kubernetes v1.20版本，删除`metadata.selflink`字段。
-   创建云盘默认打上ClusterID的Tag。
-   使用Quota配额的情况下，支持NAS的扩容。

|此次升级不会对业务造成影响。|

**2021年01月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.1.1|registry.cn-hangzhou.aliyuncs.com/acs/csi-provisioner:v1.6.0-b6f763a43-aliyun|2021年01月13日|-   支持DBFS卷。
-   默认启用卷监控。
-   支持QuotaPath类型的本地卷。
-   支持VolumeSnapshot List功能。
-   NAS卷支持配额集。
-   支持按用户自定义磁盘类型。
-   支持NAS Losetup类型卷。

|此次升级不会对业务造成影响。|

**2020年11月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.1.0|registry.cn-hangzhou.aliyuncs.com/acs/csi-provisioner:v1.6.0-b6f763a43-aliyun|2020年11月02日|-   更新部署模板并将驱动程序合并到一个容器中。
-   修改极速NAS创建子目录卷失败的问题。
-   支持Kubernetes 1.18。
-   创建NAS卷时支持添加标签。

|此次升级不会对业务造成影响。|

**2020年08月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.7|registry.cn-hangzhou.aliyuncs.com/acs/csi-provisioner:v1.4.0-aliyun|2020年08月05日|-   修复磁盘快照创建问题。
-   修复NAS动态创建时，挂载残留导致的创建失败问题。
-   当插件启动时，修复BDF节点检查逻辑。
-   删除使用UUID获取设备路径的功能。

|此次升级不会对业务造成影响。|

**2020年07月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.6|registry.cn-hangzhou.aliyuncs.com/acs/csi-provisioner:v1.4.0-aliyun|2020年07月13日|-   支持EBS快照及恢复到Beta版。
-   支持极速NAS文件系统数据卷的创建与删除。
-   如果在PV中配置，则支持EBS类型卷的Config SysConfig。
-   修复块卷的双重装载问题，特别是在BDF模式下。
-   支持EBS、NAS卷使用内网域名访问API。
-   升级CPFS驱动程序并移除对内核的依赖。

|此次升级不会对业务造成影响。|

**2020年04月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.5|registry.cn-hangzhou.aliyuncs.com/acs/csi-provisioner:v1.4.0-aliyun|2020年04月20日|-   支持EBS在删除卷之前进行卸载。
-   自动创建云盘策略更新：首先创建SSD盘，如果没有SSD盘则创建高效云盘。
-   支持使用UUID作为高优先级查找EBS卷的设备。
-   更新托管Kubernetes集群中的身份验证管理。
-   支持使用STS连接OSS Bucket。
-   更新EBS DuplicateMountPoint多重挂载问题。
-   支持在卷连接后使用BDF协议绑定EBS。

|此次升级不会对业务造成影响。|

**2020年02月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.4|registry.cn-hangzhou.aliyuncs.com/acs/csi-provisioner:v1.4.0-aliyun|2020年02月18日|-   支持无外部网络的CSI集群。
-   修复EBS Mountpath检查问题。

|此次升级不会对业务造成影响。|

**2019年12月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.2|registry.cn-hangzhou.aliyuncs.com/acs/csi-provisioner:v1.2.2-aliyun|2019年12月20日|-   支持提供EBS PV名称作为磁盘ID，与Flexvolume相同。
-   支持在MKFS Stage中为EBS卷配置装载选项。
-   支持将MountOptions选项设置为优先于NAS卷中的VolumeAttribute选项。
-   在OSS连接器中，支持检查OSS装载选项是否合法。
-   支持将Bucket的OSS子路径挂载为卷。
-   支持使用卷拓扑进行LVM动态配置。

|此次升级不会对业务造成影响。|

