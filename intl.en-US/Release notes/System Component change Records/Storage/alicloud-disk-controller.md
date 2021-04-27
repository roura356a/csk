---
keyword: [alicloud-disk-controller, changes to the alicloud-disk-controller plug-in]
---

# alicloud-disk-controller

Disks can be automatically created by using alicloud-disk-controller. This topic lists the latest changes to the alicloud-disk-controller plug-in.

## July 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.14.8.51|registry.cn-hangzhou.aliyuncs.com/acs/alicloud-disk-controller:v1.14.8.51-842f0a81-aliyun|July 9, 2020|-   Region-specific domain names are supported.
-   The base image of Alpine Linux is upgraded to V3.11.6. |

## April 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.14.8.44|registry.cn-hangzhou.aliyuncs.com/acs/alicloud-disk-controller:v1.14.8.44-c23b62c5-aliyun|April 14, 2020|-   Permissions on managed Kubernetes clusters are limited.
-   The rule to create disks in a StorageClass of the available type is modified. The system attempts to create an SSD first. If the attempt fails, the system attempts to create an ESSD. |

## March 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.14.8.37|registry.cn-hangzhou.aliyuncs.com/acs/alicloud-disk-controller:v1.14.8.37-bd3fd891-aliyun|March 6, 2020|Region-specific domain names are supported.|

## August 2019

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.12.6.21|registry.cn-hangzhou.aliyuncs.com/acs/alicloud-disk-controller:v1.12.6.21-54d91d6-aliyun|August 6, 2019|-   The template is updated and StorageClasses that use ESSDs are supported.
-   Leader Selector is no longer available to use.
-   The following issue is fixed: When the persistent volume claim \(PVC\) is in the Lost state, duplicate persistent volumes \(PVs\) are created.
-   Bring Your Own Key \(BYOK\) encryption for disks is supported. |

## February 2019

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.11.2.5|registry.cn-hangzhou.aliyuncs.com/acs/alicloud-disk-controller:v1.11.2.5-1bea041-aliyun|February 18, 2019|The topology feature is added to StorageClasses and the WaitForFirstConsumer mode is supported.|

## September 2018

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.10.4|registry.cn-hangzhou.aliyuncs.com/acs/alicloud-disk-controller:v1.10.4-1847e0f-aliyun|September 13, 2018|Zone-based round robin is supported in StorageClass.|

## July 2018

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.10.4|registry.cn-hangzhou.aliyuncs.com/acs/alicloud-disk-controller:v1.10.4-821d9c2-aliyun|July 24, 2018|Disk encryption is supported.|

