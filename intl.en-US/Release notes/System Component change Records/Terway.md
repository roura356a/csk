---
keyword: Release notes for Terway
---

# Terway

This topic lists the latest changes to the Terway plug-in.

## September 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.237-g6a0f948-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.237-g6a0f948-aliyun|September 16, 2020|The process to associate elastic network interfaces \(ENIs\) with pods is optimized.|No impact on workloads. |

## August 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.221-g8d6386a-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.221-g8d6386a-aliyun|August 11, 2020|IPVLAN and the extended Berkeley Packet Filter \(eBPF\) are supported to enable network virtualization. This feature is available to only users in the whitelist. If you are not included in the whitelist, to use this feature, click [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).|No impact on workloads. |
|v1.0.10.213-g27145cc-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.213-g27145cc-aliyun|August 4, 2020|The following issue is fixed: A pod is not accessible due to occasional ENI failures.|No impact on workloads. |

## July 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.208-gf3144bf-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.208-gf3144bf-aliyun|July 20, 2020|-   The following issue is fixed: policy-based routes for high-density servers are leaked.
-   API calls over internal networks are supported.
-   The following issue is fixed: When the number of VSwitches reaches the upper limit, IP addresses cannot be assigned to pods.
-   The error report operation that returns Container Network Interface \(CNI\) errors is optimized.

|No impact on workloads. |
|v1.0.10.211-gef088a4-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.211-gef088a4-aliyun|July 24, 2020|The feature to add tags of cluster IDs to ENIs is supported.|No impact on workloads. |

## April 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.156-g8660a0f-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.156-g8660a0f-aliyun|April 22, 2020|-   The efficiency for data to be cached through ENIs is improved.
-   Felix is upgraded to V3.5.8.
-   The feature to reclaim network resources from pods is supported. The pods are in the Completed or Failed state.

|No impact on workloads. |

## February 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.139-g14a4f84-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.139-g14a4f84-aliyun|February 12, 2020|The following issue is fixed: A timeout error may occur when you request to create pods.|No impact on workloads.|

## January 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.133-g001396b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.133-g001396b-aliyun|January 10, 2020|-   The feature to disable NetworkPolicy is supported.
-   Clusters in One ENI for Multi-Pod mode support IPVLAN to enable network virtualization.

|No impact on workloads.|

## December 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.122-gd0be015-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.122-gd0be015-aliyun|December 24, 2019|The efficiency of IP address allocation in One ENI for Multi-Pod mode is improved.|No impact on workloads.|

## October 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.100-g92a3fa5-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.100-g92a3fa5-aliyun|October 11, 2019|The following issue is fixed: When a large number of jobs concurrently request resources, the host node is not ready.|No impact on workloads.|

## August 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.9.20-g35ae000-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.9.20-g35ae000-aliyun|August 23, 2019|Kubernetes 1.14.6 is supported.|No impact on workloads.|

## April 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.9.15-g3957085-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.9.15-g3957085-aliyun|April 11, 2019|Occasional failures during the upgrade of Terway are fixed.|No impact on workloads.|

## March 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.9.14-ga0346bb-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.9.14-ga0346bb-aliyun|March 28, 2019|-   The following issue is fixed: When traffic throttling applies to the meta server, Terway fails to obtain the ENI information.
-   The following issue is fixed: The **failed to move veth to host netns: file exists** error message is returned when a container is created.
-   Periodic scanning to check the status of ENIs is supported. ENIs that are abnormally released will be periodically recycled.
-   Health checks are optimized. The TCP port check is performed instead of the HTTP path check.

|No impact on workloads.|

