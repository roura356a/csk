---
keyword: [csi-plugin, csi-plugin组件变更记录]
---

# csi-plugin

csi-plugin支持数据卷的挂载、卸载功能。ACK默认提供云盘、NAS、OSS三种存储卷的挂载能力。本文介绍csi-plugin组件相关内容的最新动态。

## 组件介绍

阿里云csi-plugin组件遵循标准CSI规范，提供了EBS、NAS、OSS等类型阿里云云存储服务的挂载能力。自ACK 1.16版集群开始，部署集群时会默认安装最新版本的CSI组件，您将可以直接通过csi-plugin插件使用阿里云存储服务。csi-plugin提供了数据卷的全生命周期管理，包括数据卷的创建、挂载、卸载、删除及扩容等服务。

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
|v1.18.8.47-06405694-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.18.8.47-06405694-aliyun|2021年07月06日|-   修复CNFS无法扩容NAS的问题。
-   使用Alibaba Cloud Linux 3镜像部署的节点支持挂载OSS。

|此次升级不会对业务造成影响。|

**2021年06月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.18.8.47-30ba5d25-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.18.8.47-30ba5d25-aliyun|2021年06月25日|-   支持CNFS创建PV。
-   VolumeSnapshot、 VolumeSnapshotClass及VolumeSnapshotContent 的apiVersion同时支持`snapshot.storage.k8s.io/v1`及`snapshot.storage.k8s.io/v1beta1`，请及时升级至`snapshot.storage.k8s.io/v1`版本。
-   升级csi-snapshotter及snapshot-controller组件版本至4.0.0。
-   修复List Snapshot偶发的Token过期问题。

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
|v1.18.8.46-afb19e46-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.18.8.46-afb19e46-aliyun|2021年04月08日|-   支持NAS监控。
-   支持青岛地域使用云盘。

|此次升级不会对业务造成影响。|

**2021年01月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.18.8.45-1c5d2cd1-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.18.8.45-1c5d2cd1-aliyun|2021年01月13日|-   默认启用卷监控。
-   支持VolumeSnapshot List功能。
-   NAS卷支持配额集。
-   支持按用户自定义磁盘类型。

|此次升级不会对业务造成影响。|

**2020年11月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.16.9.43-f36bb540-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.16.9.43-f36bb540-aliyun|2020年11月02日|-   更新部署模板并将驱动程序合并到一个容器中。
-   修改极速NAS创建子目录卷失败的问题。
-   支持Kubernetes 1.18。
-   创建NAS卷时支持添加标签。

|此次升级不会对业务造成影响。|

**2020年08月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.14.8.41-9efe2ede-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.14.8.41-9efe2ede-aliyun|2020年08月05日|-   修复磁盘快照创建问题。
-   修复NAS动态创建时，挂载残留导致的创建失败问题。
-   当插件启动时，修复BDF节点检查逻辑。
-   删除使用UUID获取设备路径。

|此次升级不会对业务造成影响。|

**2020年07月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.14.8.40-146fd1d8-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.14.8.40-146fd1d8-aliyun|2020年07月13日|-   支持EBS快照及恢复到Beta版。
-   支持极速NAS文件系统数据卷的创建与删除。
-   如果在PV中配置，则支持EBS类型卷的Config SysConfig。
-   修复块卷的双重装载问题，特别是在BDF模式下。
-   支持EBS、NAS卷使用内网域名访问API。
-   升级CPFS驱动程序并移除对内核的依赖。

|此次升级不会对业务造成影响。|

**2020年04月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.14.8.38-fe611ad1-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.14.8.38-fe611ad1-aliyun|2020年04月20日|-   支持EBS在删除卷之前进行卸载。
-   自动创建云盘策略更新：首先创建SSD盘，如果没有SSD盘则创建高效云盘。
-   支持使用UUID作为高优先级查找EBS卷的设备。
-   更新托管Kubernetes集群中的身份验证管理。
-   支持使用STS连接OSS Bucket。
-   更新EBS DuplicateMountPoint多重挂载问题。
-   支持在卷连接后使用BDF协议绑定EBS。

|此次升级不会对业务造成影响。|

**2020年03月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.14.8.36-93f2b131-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.14.8.36-93f2b131-aliyun|2020年03月14日|-   升级Ossfs，支持KMS及Byok。
-   支持Kubernetes 1.16。
-   支持用控制器服务器连接EBS。
-   支持获取EBS的度量值。

|此次升级不会对业务造成影响。|

**2020年02月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.14.8.37-aae7f495-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.14.8.37-aae7f495-aliyun|2020年02月18日|-   支持无外部网络的CSI集群。
-   修复EBS Mountpath检查问题。

|此次升级不会对业务造成影响。|

**2019年12月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.14.8.32-c77e277b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.14.8.32-c77e277b-aliyun|2019年12月20日|-   支持提供EBS PV名称作为磁盘ID，与Flexvolume相同。
-   支持在MKFS Stage中为EBS卷配置装载选项。
-   支持将MountOptions选项设置为优先于NAS卷中的VolumeAttribute选项。
-   在OSS连接器中，支持检查OSS装载选项是否合法。
-   支持将Bucket的OSS子路径挂载为卷。
-   支持使用卷拓扑进行LVM动态配置。

|此次升级不会对业务造成影响。|
|v1.14.5.60-5318afe-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.14.5.60-5318afe-aliyun|2019年12月20日|-   支持配置NAS卷的子路径及文件系统类型。
-   支持在配置磁盘卷时设置磁盘标签。
-   在Storageclass中，支持多区域集群场景下配置磁盘卷的多区域。
-   支持在配置磁盘卷时设置KMS身份验证。
-   支持扩展磁盘卷（Alpha功能）。
-   支持在配置磁盘卷时使用拓扑感知。

|此次升级不会对业务造成影响。|

