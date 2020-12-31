---
keyword: release notes for Terway
---

# Terway

This topic lists the latest changes to the Terway plug-in.

## December 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.263-gdbe50a9-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.263-gdbe50a9-aliyun|December 3, 2020|Fixes the anomalies of Terway in One ENI per Pod mode.|This upgrade does not affect your workloads. |

## November 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.261-g8342155-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.261-g8342155-aliyun|November 27, 2020|-   Supports to synchronously call the Elastic Compute Service \(ECS\) service.
-   Fixes the issue where error messages are returned when the Container Network Interface \(CNI\) plug-in is used.

|This upgrade does not affect your workloads. |
|v1.0.10.250-gb7bb10a-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.250-gb7bb10a-aliyun|November 23, 2020|-   Fixes the issue where a pod cannot connect to the network when the elastic network interface \(ENI\) driver fails to be loaded.
-   Fixes the status issue of the IP addresses allocated by ENI when the API is throttled for ENI.

|This upgrade does not affect your workloads. |

## October 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.247-g4cb77d0-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.247-g4cb77d0-aliyun|October 26, 2020|Supports Elastic Compute Service \(ECS\) instances on dedicated hosts.|This upgrade does not affect your workloads. |

## September 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.237-g6a0f948-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.237-g6a0f948-aliyun|September 16, 2020|Optimizes the process to associate elastic network interfaces \(ENIs\) with pods.|This upgrade does not affect your workloads. |

## August 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.221-g8d6386a-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.221-g8d6386a-aliyun|August 11, 2020|Supports IPVLAN and the extended Berkeley Packet Filter \(eBPF\) for network virtualization. This feature is available to only users in the whitelist. If you are not included in the whitelist, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).|This upgrade does not affect your workloads. |
|v1.0.10.213-g27145cc-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.213-g27145cc-aliyun|August 4, 2020|Fixes the issue where a pod cannot connect to the network due to occasional elastic network interface \(ENI\) failures.|This upgrade does not affect your workloads. |

## July 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.208-gf3144bf-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.208-gf3144bf-aliyun|July 20, 2020|-   Fixes the issue where Ingresses are leaked when too many Ingress rules are configured.
-   Supports API calls over internal networks.
-   Fixes the issue where IP addresses cannot be assigned to pods when the number of vSwitches reaches the upper limit.
-   Optimizes the error report interface that returns container network interface \(CNI\) errors.

|This upgrade does not affect your workloads. |
|v1.0.10.211-gef088a4-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.211-gef088a4-aliyun|July 24, 2020|Allows you to add tags of cluster IDs to elastic network interfaces \(ENIs\).|This upgrade does not affect your workloads. |

## April 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.156-g8660a0f-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.156-g8660a0f-aliyun|April 22, 2020|-   Improves the efficiency of caching through elastic network interfaces \(ENIs\).
-   Upgrades Felix to V3.5.8.
-   Allows you to reclaim network resources from pods that are in the Completed\|Failed state.

|This upgrade does not affect your workloads. |

## February 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.139-g14a4f84-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.139-g14a4f84-aliyun|February 12, 2020|Fixes the issue where pod creation requests occasionally time out.|This upgrade does not affect your workloads.|

## January 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.133-g001396b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.133-g001396b-aliyun|January 10, 2020|-   Allows you to disable the NetworkPolicy feature.
-   Clusters in One ENI for Multi-Pod mode support IPVLAN for network visualization.

|This upgrade does not affect your workloads.|

## December 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.122-gd0be015-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.122-gd0be015-aliyun|December 24, 2019|Optimizes the efficiency of IP address allocation in One ENI for Multi-Pod mode.|This upgrade does not affect your workloads.|

## October 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.100-g92a3fa5-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.100-g92a3fa5-aliyun|October 11, 2019|Fixes the issue where the host node is not ready when a large number of jobs are concurrently requesting resources.|This upgrade does not affect your workloads.|

## August 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.9.20-g35ae000-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.9.20-g35ae000-aliyun|August 23, 2019|Supports Kubernetes 1.14.6.|This upgrade does not affect your workloads.|

## April 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.9.15-g3957085-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.9.15-g3957085-aliyun|April 11, 2019|Fixes occasional failures during the upgrade of Terway.|This upgrade does not affect your workloads.|

## March 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.9.14-ga0346bb-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.9.14-ga0346bb-aliyun|March 28, 2019|-   Fixes the issue where Terway fails to obtain the elastic network interface \(ENI\) information when throttling is applied to the meta server.
-   Fixes the issue where the **failed to move veth to host netns: file exists** error is reported during container creation.
-   Supports periodic scanning to check the status of ENIs. ENIs that are abnormally released will be periodically recycled.
-   Optimizes health checks. TCP port check is performed instead of HTTP path check.

|This upgrade does not affect your workloads.|

