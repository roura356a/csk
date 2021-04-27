---
keyword: [Flexvolume, release notes of Flexvolume]
---

# Flexvolume

Volumes can be mounted and unmounted by using FlexVolume. By default, Container Service for Kubernetes \(ACK\) allows you to mount the following types of volumes: disks, Apsara File Storage NAS \(NAS\) file systems, and Object Storage Service \(OSS\) buckets. This topic lists the latest changes to Flexvolume.

## July 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.14.8.109|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.14.8.109-649dc5a-aliyun|July 24, 2020|Disks that are created from snapshots can be used as persistent volumes \(PVs\).|
|v1.14.8.104|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.14.8.104-24bde93-aliyun|July 9, 2020|-   The base image of Alpine Linux is upgraded to V3.11.6.
-   The internal endpoints of the China \(Hong Kong\) and China South 1 Finance regions are supported.
-   The logic for creating subdirectories in extreme NAS file systems is optimized. |

## April 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.14.8.88|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.14.8.88-00a6585-aliyun|April 14, 2020|-   The following issue is fixed: When the sizes are the same, the disks cannot be expanded.
-   The following issue is fixed: When the system mounts disks, the `/dev/vd* not exist` error occurs.
-   Permissions on managed Kubernetes clusters are limited. |

## March 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.14.8.66|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.14.8.66-1d0843f-aliyun|March 6, 2020|-   The deployment of the Cloud Paralleled File System \(CPFS\) driver is updated.
-   OSS buckets can be mounted to subdirectories.
-   A private API endpoint is supported. |

## October 2019

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.14.6.15|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.14.6.15-8d3b7e7-aliyun|October 21, 2019|-   Disks can be automatically expanded.
-   Monitoring of disk volumes is supported.
-   The CPFS driver is updated. |

## August 2019

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.12.6.52|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.12.6.52-f6604e5-aliyun|August 20, 2019|The NAS Mount Config configuration is supported. The optimal kernel settings are used by default.|

## April 2019

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.12.6.16|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.12.6.16-1f4c6cb-aliyun|April 16, 2019|-   The Sysctl configuration is added for NAS file systems.
-   Waitforattach is added for disks. |
|v1.12.6.11|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.12.6.11-ab46951-aliyun|April 9, 2019|-   The CPFS driver is updated.
-   Disks are locked when they are detached. This way, disks are not concurrently mounted and drive letters are not removed.
-   The limits on `-f` are updated when the NAS file systems are unmounted.
-   NAS file systems support NFSv3 by default.
-   AccessKey pairs can be entered in Secret mode for OSS buckets. |

## December 2018

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.11.2.32|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.11.2.32-af2d48c-aliyun|December 10, 2018|-   The following issue is fixed: The system restarts kubelet along with OSS buckets.
-   The waiting period for mounting disks is increased. |

## October 2018

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.11.2.5|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.11.2.5-85c062f-aliyun|October 18, 2018|The following issue is fixed: Disks are concurrently mounted.|
|v1.11.2|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.11.2-9139592-aliyun|October 17, 2018|-   The following issue is fixed: Multiple NAS file systems are mounted in high concurrent mode.
-   The following issue is fixed: The process of mounting disks is time-consuming and the Device parameter is empty. |

## September 2018

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.10.4|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.10.4-bdab325-aliyun|September 27, 2018|The following issue is fixed: Disks fail to be unmounted.|

## August 2018

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.10.4|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.10.4-dfe877b-aliyun|August 22, 2018|The NFS Options configuration is supported.|

## May 2018

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.7|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.9.7-42e8198-aliyun|May 31, 2018|The following issue is fixed: The system restarts kubelet along with disks.|

## February 2018

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.8.4|registry.cn-hangzhou.aliyuncs.com/acs/Flexvolume:v1.8.4-bcfda92-aliyun|February 05, 2018|-   Volumes that use OSS buckets, NAS file systems, and disks are supported by default.
-   Authentication method: OSS buckets support AccessKey pairs and disks support RAM roles.
-   When an ACK cluster is created, Flexvolume is automatically installed. |

