---
keyword: [csi-plugin, csi-plugin release notes]
---

# csi-plugin

You can use csi-plugin to mount and unmount volumes. Container Service for Kubernetes \(ACK\) allows you to mount the following types of volumes by using csi-plugin: disks, Apsara File Storage NAS \(NAS\) file systems, and Object Storage Service \(OSS\) buckets. This topic describes the release notes for csi-plugin.

## Introduction

The csi-plugin component provided by Alibaba Cloud follows the standard specification of Container Storage Interface \(CSI\) and enables you to mount storage media such as Elastic Block Storage \(EBS\) devices, NAS file systems, and OSS buckets. By default, the latest version of csi-plugin is installed when you create ACK clusters of Kubernetes 1.16 or later. You can directly use csi-plugin to mount storage services provided by Alibaba Cloud after your cluster is created. The csi-plugin component supports volume lifecycle management. You can use csi-plugin to create, mount, unmount, delete, and expand volumes.

## Usage notes

For more information, see [CSI overview](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Storage overview.md).

## Release notes

**August 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.18.8.51-c504ef45-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.18.8.51-c504ef45-aliyun|2021-08-19|-   The time parameter for the recycle bin is added to Container Network File System \(CNFS\).
-   The `apiVersion` of CNFS is changed from `v1alpha1` to `v1beta1`.
-   The issue that Object Storage Service File System \(OSSFS\) cannot synchronize data in real time is fixed.
-   By default, the Detachdisk option is disabled.

|No impact on workloads.|

**July 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.18.8.47-06405694-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.18.8.47-06405694-aliyun|2021-07-06|-   The issue that NAS file systems cannot be expanded by using CNFS is fixed.
-   OSS buckets can be mounted to nodes that are deployed by using the Alibaba Cloud Linux 3 image.

|No impact on workloads.|

**June 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.18.8.47-30ba5d25-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.18.8.47-30ba5d25-aliyun|2021-06-25|-   Persistent volumes \(PVs\) can be created by using CNFS.
-   The `snapshot.storage.k8s.io/v1` and `snapshot.storage.k8s.io/v1beta1` API versions are supported by VolumeSnapshots, VolumeSnapshotClasses, and VolumeSnapshotContents. Upgrade the API version to `snapshot.storage.k8s.io/v1` at the earliest opportunity.
-   csi-snapshotter and snapshot-controller are upgraded to V4.0.0.
-   The occasional issue that the token expires when you perform the List Snapshot operation is fixed.

|No impact on workloads.|

**May 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.18.8.47-906bd535-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.18.8.47-906bd535-aliyun|2021-05-20|-   Disk partitions can be mounted.
-   Disk partitions can be expanded.

|No impact on workloads.|

**April 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.18.8.46-afb19e46-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.18.8.46-afb19e46-aliyun|2021-04-08|-   NAS monitoring is supported.
-   Disks are supported in the China \(Qingdao\) region.

|No impact on workloads.|

**January 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.18.8.45-1c5d2cd1-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.18.8.45-1c5d2cd1-aliyun|2021-01-13|-   By default, volume monitoring is enabled.
-   The VolumeSnapshot List feature is supported.
-   Quota groups are supported by NAS volumes.
-   Custom disk types are supported.

|No impact on workloads.|

**November 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.16.9.43-f36bb540-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.16.9.43-f36bb540-aliyun|2020-11-02|-   The deployment template is updated to merge drivers into one container.
-   The issue that subdirectories fail to be created in Extreme NAS file systems is fixed.
-   Kubernetes 1.18 is supported.
-   Labels can be added to NAS volumes when you create NAS volumes.

|No impact on workloads.|

**August 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.14.8.41-9efe2ede-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.14.8.41-9efe2ede-aliyun|2020-08-05|-   The issue that snapshots cannot be created from disks is fixed.
-   The issue that dynamic provisioning of NAS volumes fails due to residual data is fixed.
-   The check logic of BDF nodes when csi-plugin is started is fixed.
-   The use of universally unique identifier \(UUID\) to obtain device paths is no longer supported.

|No impact on workloads.|

**July 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.14.8.40-146fd1d8-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.14.8.40-146fd1d8-aliyun|2020-07-13|-   Elastic Block Storage \(EBS\) snapshots are supported. You can use EBS snapshots to restore data to a beta version.
-   Extreme NAS volumes can be created and deleted.
-   The Config SysConfig parameter of EBS volumes is supported when you configure PVs.
-   The issue that block volumes are loaded twice in BDF mode is fixed.
-   EBS and NAS volumes are allowed to access APIs by using internal domain names.
-   The Cloud Paralleled File System \(CPFS\) driver is upgraded and the dependency on the kernel is removed.

|No impact on workloads.|

**April 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.14.8.38-fe611ad1-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.14.8.38-fe611ad1-aliyun|2020-04-20|-   EBS volumes can be unmounted before you delete the volumes.
-   The disk creation policy is updated. Standard SSDs are created in preference to ultra disks. Ultra disks are created only when no standard SSD is available.
-   UUID is supported as a high-priority search option to search for devices that use EBS volumes.
-   The authentication management in managed Kubernetes clusters is updated.
-   Security Token Service \(STS\) is supported to connect to OSS buckets.
-   DuplicateMountPoint errors in EBS are fixed.
-   The BDF protocol is supported to bind EBS volumes after the volumes are connected.

|No impact on workloads.|

**March 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.14.8.36-93f2b131-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.14.8.36-93f2b131-aliyun|2020-03-14|-   OSSFS is upgraded to support Key Management Service \(KMS\) and Bring Your Own Key \(BYOK\).
-   Kubernetes 1.16 is supported.
-   EBS volumes can be connected by using the controller server.
-   EBS metric values can be obtained.

|No impact on workloads.|

**February 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.14.8.37-aae7f495-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.14.8.37-aae7f495-aliyun|2020-02-18|-   Kubernetes clusters that use CSI and have no Internet access are supported.
-   The issues related to mount path checks in EBS are fixed.

|No impact on workloads.|

**December 2019**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.14.8.32-c77e277b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.14.8.32-c77e277b-aliyun|2019-12-20|-   The EBS PV name can be used as the disk ID. This feature is also supported by FlexVolume.
-   Mount options can be configured for EBS volumes in MKFS Stage.
-   Mount options can be configured to have a higher priority than the volume attributes of NAS volumes.
-   Mount options of OSS volumes can be validated in OSS connectors.
-   Subpaths of OSS buckets can be mounted as volumes.
-   Volume topology can be used to dynamically configure Logical Volume Manager \(LVM\).

|No impact on workloads.|
|v1.14.5.60-5318afe-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.14.5.60-5318afe-aliyun|2019-12-20|-   The mounted subpath and file system type can be configured for a NAS volume.
-   Labels can be added to disk volumes when you configure disk volumes.
-   Multiple regions can be configured for disk volumes that are used in multi-region clusters in the StorageClass definition.
-   KMS-based authentication can be configured when you configure disk volumes.
-   Disk volumes can be expanded. This feature is in internal preview.
-   The topology awareness feature is supported when you configure disk volumes.

|No impact on workloads.|

