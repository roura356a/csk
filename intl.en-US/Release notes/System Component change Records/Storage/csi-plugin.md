---
keyword: [csi-plugin, csi-plugin release notes]
---

# csi-plugin

You can use csi-plugin to mount and unmount volumes. Container Service for Kubernetes \(ACK\) allows you to mount the following types of storage media by using csi-plugin: disks, Apsara File Storage NAS file systems, and Object Storage Service \(OSS\) buckets. This topic lists the latest changes to csi-plugin.

## Introduction

The csi-plugin component provided by Alibaba Cloud follows the standard specification of Container Storage Interface \(CSI\) and enables you to mount storage media such as Elastic Block Storage \(EBS\) devices, NAS file systems, and OSS buckets. By default, the latest csi-plugin is installed when you create Kubernetes clusters of version 1.16 or later. You can directly use csi-plugin to mount storage services provided by Alibaba Cloud after your cluster is created. The csi-plugin component supports volume lifecycle management. You can use csi-plugin to create, mount, unmount, delete, and expand volumes.

## Usage notes

For more information, see [Overview](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Overview.md).

## Release notes

**April 2021**

|Version|Image address|Date|Description|Impact|
|-------|-------------|----|-----------|------|
|v1.1.2|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.18.8.46-afb19e46-aliyun|2021-04-08|-   NAS monitoring is supported.
-   Cloud disks are supported in the China \(Qingdao\) region.

|No impact on workloads.|

**January 2021**

|Version|Image address|Date|Description|Impact|
|-------|-------------|----|-----------|------|
|v1.1.1|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.18.8.45-1c5d2cd1-aliyun|2021-01-13|-   By default, volume monitoring is enabled.
-   The VolumeSnapshot List feature is supported.
-   Quota groups are supported by NAS volumes.
-   Custom disk types are supported.

|No impact on workloads.|

**November 2020**

|Version|Image address|Date|Description|Impact|
|-------|-------------|----|-----------|------|
|v1.1.0|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.16.9.43-f36bb540-aliyun|2020-11-02|-   The deployment template is updated to allow drivers to be merged into one container.
-   The issue that subdirectories fail to be created in Extreme NAS volumes is fixed.
-   Kubernetes 1.18 is supported.
-   Labels can be added to NAS volumes when you create NAS volumes.

|No impact on workloads.|

**August 2020**

|Version|Image address|Date|Description|Impact|
|-------|-------------|----|-----------|------|
|v1.0.7|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.14.8.41-9efe2ede-aliyun|2020-08-05|-   The issue that occurs when you create snapshots from disks is fixed.
-   The issue that dynamic NAS volumes fail to be created due to residual data is fixed.
-   The check logic of BDF nodes when the plug-in is started is fixed.
-   The use of universally unique identifier \(UUID\) to obtain device paths is no longer supported.

|No impact on workloads.|

**July 2020**

|Version|Image address|Date|Description|Impact|
|-------|-------------|----|-----------|------|
|v1.0.6|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.14.8.40-146fd1d8-aliyun|2020-07-13|-   EBS snapshots are supported. You can use EBS snapshots to restore data to the beta version.
-   Extreme NAS volumes are supported. You can create and delete Extreme NAS volumes.
-   The Config SysConfig of EBS volumes is supported when you configure PVs.
-   The issue that block volumes are loaded twice in the BDF mode is fixed.
-   EBS and NAS volumes are allowed to access APIs by using the internal domain.
-   The Cloud Paralleled File System \(CPFS\) driver is upgraded and the kernel dependency is removed.

|No impact on workloads.|

**April 2020**

|Version|Image address|Date|Description|Impact|
|-------|-------------|----|-----------|------|
|v1.0.5|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.14.8.38-fe611ad1-aliyun|2020-04-20|-   EBS volumes can be unmounted before you delete the volumes.
-   The disk provisioning policy is updated. SSDs are created in preference to ultra disks. Ultra disks are created only when no SSD is available.
-   UUID is supported to search devices that use EBS volumes.
-   The authentication management in managed Kubernetes clusters is updated.
-   Security Token Service \(STS\) is supported to connect to OSS buckets.
-   The issue of DuplicateMountPoint in EBS is fixed.
-   The BDF protocol is supported to bind EBS volumes after the volumes are connected.

|No impact on workloads.|

**March 2020**

|Version|Image address|Date|Description|Impact|
|-------|-------------|----|-----------|------|
|v1.0.3|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.14.8.36-93f2b131-aliyun|2020-03-14|-   OSSFS is upgraded to support Key Management Service \(KMS\) and Bring Your Own Key \(BYOK\).
-   Kubernetes 1.16 is supported.
-   EBS volumes can be connected by using the controller server.
-   EBS metrics can be obtained.

|No impact on workloads.|

**February 2020**

|Version|Image address|Date|Description|Impact|
|-------|-------------|----|-----------|------|
|v1.0.4|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.14.8.37-aae7f495-aliyun|2020-02-18|-   CSI clusters that have no Internet access are supported.
-   The issue of Mountpath check in EBS is fixed.

|No impact on workloads.|

**December 2019**

|Version|Image address|Date|Description|Impact|
|-------|-------------|----|-----------|------|
|v1.0.2|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.14.8.32-c77e277b-aliyun|2019-12-20|-   The EBS PV name can be used as the disk ID. This feature is also supported by Flexvolume.
-   Mount options can be configured for EBS volumes in MKFS Stage.
-   MountOptions can be configured to have higher priority than VolumeAttribute in NAS volumes.
-   Mount options of OSS volumes can be validated in OSS connectors.
-   Subdirectories of OSS buckets can be mounted as volumes.
-   Volume topology can be used to dynamically configure Logical Volume Manager \(LVM\).

|No impact on workloads.|
|v1.0.1|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.14.5.60-5318afe-aliyun|2019-12-20|-   The subdirectory and file system type of NAS volumes can be configured.
-   Labels can be added to disk volumes when you configure disk volumes.
-   Multiple regions can be configured for disk volumes that are used in multi-region clusters in the StorageClass definition.
-   KMS authentication can be configured when you configure disk volumes.
-   Disk volumes can be expanded.
-   The topology awareness feature is supported when you configure disk volumes.

|No impact on workloads.|

