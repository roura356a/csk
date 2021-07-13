---
keyword: [alicloud-disk-controller, changes to the alicloud-disk-controller plug-in]
---

# alicloud-disk-controller

Disks can be automatically created by using alicloud-disk-controller. This topic lists the latest changes to the alicloud-disk-controller plug-in.

## Introduction

In a computer, the disk controller is a circuit that allows the CPU to communicate with other computer disks, such as a floppy disk, hard disk, or some other type of disk drives. The basic operations that you can perform on disk controllers include various read and write operations. Different types of disk controllers can be used as one component in a computer. The alicloud-disk-controller plug-in can automatically create disks.

## Instruction

For more information about how to install alicloud-disk-controller, see [t15789.md\#section\_2yj\_ugs\_tsg](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Install and upgrade FlexVolume.md).

## Release notes

**April 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.16.9.55|registry.cn-hangzhou.aliyuncs.com/acs/alicloud-disk-controller:v1.16.9.55-c43698d4-aliyun|2021-04-05|Kubernetes 1.20 is supported. The selflink field is deleted in metadata.|No impact on workloads.|

**July 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.14.8.51|registry.cn-hangzhou.aliyuncs.com/acs/alicloud-disk-controller:v1.14.8.51-842f0a81-aliyun|2020-07-09|-   Region-specific domain names are supported.
-   The base image of Alpine Linux is upgraded to V3.11.6.

|No impact on workloads.|

**April 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.14.8.44|registry.cn-hangzhou.aliyuncs.com/acs/alicloud-disk-controller:v1.14.8.44-c23b62c5-aliyun|2020-04-14|-   Permissions on managed Kubernetes clusters are limited.
-   The rule to create disks in a StorageClass of the available type is modified. The system attempts to create an SSD first. If the attempt fails, the system attempts to create an ESSD.

|No impact on workloads.|

**March 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.14.8.37|registry.cn-hangzhou.aliyuncs.com/acs/alicloud-disk-controller:v1.14.8.37-bd3fd891-aliyun|2020-03-06|Region-specific domain names are supported.|No impact on workloads.|

**August 2019**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.12.6.21|registry.cn-hangzhou.aliyuncs.com/acs/alicloud-disk-controller:v1.12.6.21-54d91d6-aliyun|2019-08-06|-   The template is updated and StorageClasses that use ESSDs are supported.
-   Leader Selector is no longer available.
-   The following issue is fixed: When the persistent volume claim \(PVC\) is in the Lost state, duplicate persistent volumes \(PVs\) are created.
-   Bring Your Own Key \(BYOK\) encryption for disks is supported.

|No impact on workloads.|

**February 2019**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.11.2.5|registry.cn-hangzhou.aliyuncs.com/acs/alicloud-disk-controller:v1.11.2.5-1bea041-aliyun|2019-02-18|The topology feature is added to StorageClasses and the WaitForFirstConsumer mode is supported.|No impact on workloads.|

**September 2018**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.10.4|registry.cn-hangzhou.aliyuncs.com/acs/alicloud-disk-controller:v1.10.4-1847e0f-aliyun|2018-09-13|Zone-based round robin is supported in StorageClasses.|No impact on workloads.|

**July 2018**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.10.4|registry.cn-hangzhou.aliyuncs.com/acs/alicloud-disk-controller:v1.10.4-821d9c2-aliyun|2018-07-24|Disk encryption is supported.|No impact on workloads.|

