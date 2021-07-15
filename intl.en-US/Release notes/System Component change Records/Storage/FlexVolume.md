---
keyword: [FlexVolume, FlexVolume release notes]
---

# FlexVolume

Volumes can be mounted and unmounted by using FlexVolume. By default, Container Service for Kubernetes \(ACK\) allows you to mount the following types of volume: disk volumes, Apsara File Storage NAS \(NAS\) volumes, and Object Storage Service \(OSS\) volumes. This topic lists the latest changes to FlexVolume.

## Introduction

FlexVolume is a traditional mechanism developed by the Kubernetes community to extend the storage of Kubernetes clusters. ACK supports FlexVolume. FlexVolume consists of the following parts:

-   FlexVolume: Mounts and unmounts volumes. By default, ACK allows you to mount the following types of volume: disk volumes, NAS volumes, and OSS volumes.
-   Disk-Controller: automatically creates disk volumes.
-   Nas-Controller: automatically creates NAS volumes.

For more information, see [Overview](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Overview.md).

## Usage notes

For more information about how to use FlexVolume, see [Storage overview](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Storage overview.md).

## Release notes

**May 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.18.8.166|registry.cn-hangzhou.aliyuncs.com/acs/flexvolume:v1.18.8.166-b39c52a-aliyun|2021-05-27|-   By default, disk monitoring is enabled.
-   Server Message Block \(SMB\) file systems can be mounted to Windows containers.

|No impact on workloads|

**July 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.14.8.109|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.14.8.109-649dc5a-aliyun|2020-07-24|Disks that are created from snapshots can be used as persistent volumes \(PVs\).|No impact on workloads|
|v1.14.8.104|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.14.8.104-24bde93-aliyun|2020-07-09|-   Alpine Linux is upgraded to V3.11.6 for base images.
-   The internal endpoints of the China \(Hong Kong\) and China South 1 Finance regions are supported.
-   The logic for creating subdirectories in Extreme NAS file systems is optimized.

|No impact on workloads|

**April 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.14.8.88|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.14.8.88-00a6585-aliyun|2020-04-14|-   The issue that a disk cannot be expanded when the target size is the same as the current size is fixed.
-   The issue that the `/dev/vd* not exist` error is returned when you mount a disk is fixed.
-   Permissions on managed Kubernetes clusters are limited.

|No impact on workloads|

**March 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.14.8.66|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.14.8.66-1d0843f-aliyun|2020-03-06|-   The deployment method of the Cloud Paralleled File System \(CPFS\) driver is updated.
-   Subdirectories of OSS buckets can be mounted to containers.
-   Private API endpoints are supported.

|No impact on workloads|

**October 2019**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.14.6.15|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.14.6.15-8d3b7e7-aliyun|2019-10-21|-   Disks can be automatically expanded.
-   Monitoring of disk volumes is supported.
-   The CPFS driver is updated.

|No impact on workloads|

**August 2019**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.12.6.52|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.12.6.52-f6604e5-aliyun|2019-08-20|The NAS Mount Config configuration is supported. By default, the optimal kernel settings are used.|No impact on workloads|

**April 2019**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.12.6.16|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.12.6.16-1f4c6cb-aliyun|2019-04-16|-   The Sysctl configuration is added for NAS file systems.
-   Waitforattach operations can be performed on disks.

|No impact on workloads|
|v1.12.6.11|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.12.6.11-ab46951-aliyun|2019-04-09|-   The CPFS driver is upgraded.
-   Disks are locked when they are detached. This way, disks are not concurrently mounted and drive letters are not removed.
-   The limits on the `kubectl -f` command are updated when NAS file systems are unmounted.
-   By default, NFSv3 is supported by NAS file systems.
-   AccessKey pairs can be specified by using Secrets when you mount OSS buckets.

|No impact on workloads|

**December 2018**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.11.2.32|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.11.2.32-af2d48c-aliyun|2018-12-10|-   The issue that the OSSFS driver is restarted along with kubelet is fixed.
-   The waiting period for mounting disks is increased.

|No impact on workloads|

**October 2018**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.11.2.5|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.11.2.5-85c062f-aliyun|2018-10-18|The issue that disks are concurrently mounted is fixed.|No impact on workloads|
|v1.11.2|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.11.2-9139592-aliyun|2018-10-17|-   The issue that NAS file systems are repeatedly mounted in high concurrency mode is fixed.
-   The issue that the process of mounting disks is time-consuming is fixed. The issue that the Device parameter is empty is fixed.

|No impact on workloads|

**September 2018**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.10.4|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.10.4-bdab325-aliyun|2018-09-27|The issue that disks fail to be unmounted is fixed.|No impact on workloads|

**August 2018**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.10.4|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.10.4-dfe877b-aliyun|2018-08-22|The NFS Options configuration is supported.|No impact on workloads|

**May 2018**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.9.7|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.9.7-42e8198-aliyun|2018-05-31|The issue that disks are remounted when kubelet is restarted is fixed.|No impact on workloads|

**February 2018**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.8.4|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.8.4-bcfda92-aliyun|2018-02-05|-   By default, OSS volumes, NAS volumes, and disk volumes are supported.
-   The following authentication methods are supported: OSS buckets support AccessKey pairs and disks support Resource Access Management \(RAM\) roles.
-   When an ACK cluster is created, FlexVolume is automatically installed.

|No impact on workloads|

