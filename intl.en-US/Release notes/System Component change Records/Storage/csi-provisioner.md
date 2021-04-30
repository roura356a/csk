---
keyword: [csi-provisioner, csi-provisioner release notes]
---

# csi-provisioner

csi-provisioner allows you to automatically create volumes. This topic lists the latest changes to csi-provisioner.

## Introduction

The csi-provisioner component provided by Alibaba Cloud allows you to automatically create volumes. You can use csi-provisioner to create volumes from cloud disks and Apsara File Storage NAS file systems. The Kubernetes version of the cluster must be 1.14 or later.

## Usage notes

For more information, see [Overview](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Overview.md).

## Release notes

**April 2021**

|Version|Image address|Date|Description|Impact|
|-------|-------------|----|-----------|------|
|v1.1.2|registry.cn-hangzhou.aliyuncs.com/acs/csi-provisioner:v1.6.0-e360c7e43-aliyun|2021-04-08|-   Kubernetes 1.20 is supported. The `metadata.selflink` field is deleted.
-   The ClusterID tag is automatically added to cloud disks.
-   NAS volumes can be expanded when the quota is used.

|No impact on workloads.|

**January 2021**

|Version|Image address|Date|Description|Impact|
|-------|-------------|----|-----------|------|
|v1.1.1|registry.cn-hangzhou.aliyuncs.com/acs/csi-provisioner:v1.6.0-b6f763a43-aliyun|2021-01-13|-   DBFS volumes are supported.
-   By default, volume monitoring is enabled.
-   Local volumes of the QuotaPath type are supported.
-   The VolumeSnapshot List feature is supported.
-   Quota groups are supported by NAS volumes.
-   Custom disk types are supported.
-   NAS volumes of the Losetup type are supported.

|No impact on workloads.|

**November 2020**

|Version|Image address|Date|Description|Impact|
|-------|-------------|----|-----------|------|
|v1.1.0|registry.cn-hangzhou.aliyuncs.com/acs/csi-provisioner:v1.6.0-b6f763a43-aliyun|2020-11-02|-   The deployment template is updated to allow drivers to be merged into one container.
-   The issue that subdirectories fail to be created in Extreme NAS volumes is fixed.
-   Kubernetes 1.18 is supported.
-   Labels can be added to NAS volumes when you create NAS volumes.

|No impact on workloads.|

**August 2020**

|Version|Image address|Date|Description|Impact|
|-------|-------------|----|-----------|------|
|v1.0.7|registry.cn-hangzhou.aliyuncs.com/acs/csi-provisioner:v1.4.0-aliyun|2020-08-05|-   The issue that occurs when you create snapshots from disks is fixed.
-   The issue that dynamic NAS volumes fail to be created due to residual data is fixed.
-   The check logic of BDF nodes when the plug-in is started is fixed.
-   The use of universally unique identifier \(UUID\) to obtain device paths is no longer supported.

|No impact on workloads.|

**July 2020**

|Version|Image address|Date|Description|Impact|
|-------|-------------|----|-----------|------|
|v1.0.6|registry.cn-hangzhou.aliyuncs.com/acs/csi-provisioner:v1.4.0-aliyun|2020-07-13|-   EBS snapshots are supported. You can use EBS snapshots to restore data to the beta version.
-   Extreme NAS volumes are supported. You can create and delete Extreme NAS volumes.
-   The Config SysConfig of EBS volumes is supported when you configure PVs.
-   The issue that block volumes are loaded twice in the BDF mode is fixed.
-   EBS and NAS volumes are allowed to access APIs by using the internal domain.
-   The Cloud Paralleled File System \(CPFS\) driver is upgraded and the kernel dependency is removed.

|No impact on workloads.|

**April 2020**

|Version|Image address|Date|Description|Impact|
|-------|-------------|----|-----------|------|
|v1.0.5|registry.cn-hangzhou.aliyuncs.com/acs/csi-provisioner:v1.4.0-aliyun|2020-04-20|-   EBS volumes can be unmounted before you delete the volumes.
-   The disk provisioning policy is updated. SSDs are created in preference to ultra disks. Ultra disks are created only when no SSD is available.
-   UUID is supported to search devices that use EBS volumes.
-   The authentication management in managed Kubernetes clusters is updated.
-   Security Token Service \(STS\) is supported to connect to OSS buckets.
-   The issue of DuplicateMountPoint in EBS is fixed.
-   The BDF protocol is supported to bind EBS volumes after the volumes are connected.

|No impact on workloads.|

**February 2020**

|Version|Image address|Date|Description|Impact|
|-------|-------------|----|-----------|------|
|v1.0.4|registry.cn-hangzhou.aliyuncs.com/acs/csi-provisioner:v1.4.0-aliyun|2020-02-18|-   CSI clusters that have no Internet access are supported.
-   The issue of Mountpath check in EBS is fixed.

|No impact on workloads.|

**December 2019**

|Version|Image address|Date|Description|Impact|
|-------|-------------|----|-----------|------|
|v1.0.2|registry.cn-hangzhou.aliyuncs.com/acs/csi-provisioner:v1.2.2-aliyun|2019-12-20|-   The EBS PV name can be used as the disk ID. This feature is also supported by Flexvolume.
-   Mount options can be configured for EBS volumes in MKFS Stage.
-   MountOptions can be configured to have a higher priority than VolumeAttribute in NAS volumes.
-   Mount options of OSS volumes can be validated in OSS connectors.
-   Subdirectories of OSS buckets can be mounted as volumes.
-   Volume topology can be used to dynamically configure Logical Volume Manager \(LVM\).

|No impact on workloads.|

