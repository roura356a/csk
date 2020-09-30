---
keyword: Release notes for Terway
---

# Release notes for Terway

This topic provides release notes for the Terway plug-in.

## September 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.237-g6a0f948-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.237-g6a0f948-aliyun|September 16, 2020|Optimizes the binding of elastic network interfaces \(ENIs\).|No impact on workloads. |

## August 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.221-g8d6386a-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.221-g8d6386a-aliyun|August 11, 2020|Supports IPVLAN and the extended Berkeley Packet Filter \(eBPF\) for network virtualization. This feature is available to only users in the whitelist. To use this feature, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).|No impact on workloads. |
|v1.0.10.213-g27145cc-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.213-g27145cc-aliyun|August 4, 2020|Fixes the issue where a pod cannot connect to the network due to occasional ENI failures.|No impact on workloads. |

## July 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.208-gf3144bf-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.208-gf3144bf-aliyun|July 20, 2020|-   Fixes the issue where policy-based routes for high-density servers are leaked.
-   Supports API calls over internal networks.
-   Fixes the issue where IP addresses cannot be assigned to pods when the number of VSwitches reaches the upper limit.
-   Optimizes the error report interface that returns container network interface \(CNI\) errors.

|No impact on workloads. |
|v1.0.10.211-gef088a4-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.211-gef088a4-aliyun|July 24, 2020|Supports attaching cluster IDs to ENIs.|No impact on workloads. |

## April 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.156-g8660a0f-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.156-g8660a0f-aliyun|April 22, 2020|-   Improves the cache efficiency through ENIs.
-   Upgrades Felix to V3.5.8.
-   Supports releasing network resources of pods that are in the Completed or Failed state.

|No impact on workloads. |

## February 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.139-g14a4f84-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.139-g14a4f84-aliyun|February 12, 2020|Fixes occasional timeouts during pod creation.|No impact on workloads.|

## January 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.133-g001396b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.133-g001396b-aliyun|January 10, 2020|-   Supports disabling network policies.
-   Clusters in One ENI for Multi-Pod mode support IPVLAN for network virtualization.

|No impact on workloads.|

## December 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.122-gd0be015-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.122-gd0be015-aliyun|December 24, 2019|Optimizes the efficiency of IP address allocation in One ENI for Multi-Pod mode.|No impact on workloads.|

## October 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.100-g92a3fa5-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.100-g92a3fa5-aliyun|October 11, 2019|Fixes the issue where the node is not ready when a large number of jobs are concurrently requesting resources.|No impact on workloads.|

## August 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.9.20-g35ae000-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.9.20-g35ae000-aliyun|August 23, 2019|Supports Kubernetes 1.14.6.|No impact on workloads.|

## April 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.9.15-g3957085-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.9.15-g3957085-aliyun|April 11, 2019|Fixes occasional failures during the upgrade of Terway.|No impact on workloads.|

## March 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.9.14-ga0346bb-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.9.14-ga0346bb-aliyun|March 28, 2019|-   Fixes the issue where Terway fails to obtain the ENI information when throttling is applied to the MetaServer.
-   Fixes the issue where the **failed to move veth to host netns: file exists** error is reported during container creation.
-   Supports periodic scans for ENIs. ENIs that are abnormally released are recycled on a regular basis.
-   Optimizes health checks to perform TCP port check instead of HTTP path check.

|No impact on workloads.|

