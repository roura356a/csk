---
keyword: [csi-provisioner, csi-provisioner组件变更记录]
---

# csi-provisioner

csi-provisioner支持数据卷的自动创建能力。本文介绍csi-provisioner组件的介绍、使用说明及变更记录。

## 组件介绍

阿里云csi-provisioner组件提供了数据卷的自动创建能力，目前仅支持云盘、NAS两种存储卷，要求容器服务Kubernetes为1.14及以上版本。

## 使用说明

更多信息，请参见[存储CSI](/intl.zh-CN/Kubernetes集群用户指南/存储-CSI/存储CSI概述.md)。

## 变更记录

**2021年08月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.18.8.51-c504ef45-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.18.8.51-c504ef45-aliyun|2021年08月19日|-   CNFS增加配置回收站时间参数。
-   将CNFS的`apiVersion`从`v1alpha1`改为`v1beta1`。
-   修复OSSFS无法即时同步数据问题。
-   默认关闭强制Detach云盘的选项。

|此次升级不会对业务造成影响。|

**2021年07月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.18.8.48-cd524404-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.18.8.48-cd524404-aliyun|2021年07月06日|-   修复CNFS无法扩容NAS的问题。
-   使用Alibaba Cloud Linux 3镜像部署的节点支持挂载OSS。

|此次升级不会对业务造成影响。|

**2021年06月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.18.8.47-30ba5d25-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.18.8.47-30ba5d25-aliyun|2021年06月25日|-   NAS删除`volumeCapacity`字段，通过`allowVolumeExpansion`字段控制Quota能力是否开启。
-   NAS删除`selflink`字段。

|此次升级不会对业务造成影响。|

**2021年05月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.18.8.47-906bd535-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.18.8.47-906bd535-aliyun|2021年05月20日|-   支持云盘分区挂载。
-   支持分区云盘扩容。

|此次升级不会对业务造成影响。|

**2021年04月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.6.0-e360c7e43-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-provisioner:v1.6.0-e360c7e43-aliyun|2021年04月08日|-   适配Kubernetes v1.20版本，删除`metadata.selflink`字段。
-   创建云盘默认打上ClusterID的Tag。
-   使用Quota配额的情况下，支持NAS的扩容。

|此次升级不会对业务造成影响。|

**2021年01月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.6.0-b6f763a43-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-provisioner:v1.6.0-b6f763a43-aliyun|2021年01月13日|-   支持DBFS卷。
-   默认启用卷监控。
-   支持QuotaPath类型的本地卷。
-   支持VolumeSnapshot List功能。
-   NAS卷支持配额集。
-   支持用户自定义磁盘类型。

|此次升级不会对业务造成影响。|

**2020年11月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.6.0-b6f763a43-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-provisioner:v1.6.0-b6f763a43-aliyun|2020年11月02日|-   更新部署模板并将驱动程序合并到一个容器中。
-   修改极速型NAS创建子目录卷失败的问题。
-   支持Kubernetes 1.18。
-   创建NAS卷时支持添加标签。

|此次升级不会对业务造成影响。|

**2020年08月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.4.0-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-provisioner:v1.4.0-aliyun|2020年08月05日|-   修复磁盘快照创建问题。
-   修复NAS动态创建时，挂载残留导致的创建失败问题。
-   当插件启动时，修复BDF节点检查逻辑。
-   删除使用UUID获取设备路径的功能。

|此次升级不会对业务造成影响。|

**2020年07月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.4.0-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-provisioner:v1.4.0-aliyun|2020年07月13日|-   支持EBS快照及恢复到Beta版。
-   支持极速NAS文件系统数据卷的创建与删除。
-   如果在PV中配置，则支持EBS类型卷的Config SysConfig。
-   修复块卷的双重装载问题，特别是在BDF模式下。
-   支持EBS、NAS卷使用内网域名访问API。
-   升级CPFS驱动程序并移除对内核的依赖。

|此次升级不会对业务造成影响。|

**2020年04月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.4.0-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-provisioner:v1.4.0-aliyun|2020年04月20日|-   支持EBS在删除卷之前进行卸载。
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
|v1.4.0-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-provisioner:v1.4.0-aliyun|2020年02月18日|-   支持无外部网络的CSI集群。
-   修复EBS Mountpath检查问题。

|此次升级不会对业务造成影响。|

**2019年12月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.2.2-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-provisioner:v1.2.2-aliyun|2019年12月20日|-   支持提供EBS PV名称作为磁盘ID，与Flexvolume相同。
-   支持在MKFS Stage中为EBS卷配置装载选项。
-   支持将MountOptions选项设置为优先于NAS卷中的VolumeAttribute选项。
-   在OSS连接器中，支持检查OSS装载选项是否合法。
-   支持将Bucket的OSS子路径挂载为卷。
-   支持使用卷拓扑进行LVM动态配置。

|此次升级不会对业务造成影响。|

