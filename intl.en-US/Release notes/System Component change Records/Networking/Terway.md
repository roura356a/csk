---
keyword: release notes for Terway
---

# Terway

This topic lists the latest changes to the Terway plug-in.

## February 2021

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.309-g5314eee-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.309-g5314eee-aliyun|February 5, 2021|-   Errors in the configurations of security group rules are no longer automatically fixed. The system raises only alerts when errors are detected. You can follow the suggestions to fix the errors.
-   Fixes the issue that IP addresses of pods may be reclaimed and re-assigned to the pods when the nodes are overwhelmed.

|This upgrade does not affect your workloads. |

## January 2021

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.301-g0115576-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.301-g0115576-aliyun|January 21, 2021|New features:

-   Supports custom maximum transmission units \(MTUs\).
-   Supports routing based on the host network stack in the One ENI per Pod mode.

Fixed issues:

-   Packet loss due to traffic throttling of Terway.
-   Residual IP policies.
-   Incorrect counting of the number of IP addresses provided by elastic network interfaces \(ENIs\).

|This upgrade does not affect your workloads. |

## December 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.280-gdc2cb6c-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.280-gdc2cb6c-aliyun|December 25, 2020|-   Supports automatic Terway health check and and modifies the configurations of the security groups with which ENIs are associated.
-   Supports routing based on the host network stack if IPVLAN is used for Terway.

|This upgrade does not affect your workloads. |
|v1.0.10.263-gdbe50a9-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.263-gdbe50a9-aliyun|December 3, 2020|Fixes the errors of Terway in the One ENI per Pod mode.|This upgrade does not affect your workloads. |

## November 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.261-g8342155-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.261-g8342155-aliyun|November 27, 2020|-   Supports synchronous calls to the Elastic Compute Service \(ECS\) API.
-   Fixes the issue that causes error messages to return when the Container Network Interface \(CNI\) plug-in is used.

|This upgrade does not affect your workloads. |
|v1.0.10.250-gb7bb10a-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.250-gb7bb10a-aliyun|November 23, 2020|-   Fixes the issue that a pod cannot connect to the network when the elastic network interface \(ENI\) driver fails to be loaded.
-   Fixes the status issue of IP addresses that are allocated by ENI when the ENI API is throttled.

|This upgrade does not affect your workloads. |

## October 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.247-g4cb77d0-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.247-g4cb77d0-aliyun|October 26, 2020|Supports ECS instances deployed on dedicated hosts.|This upgrade does not affect your workloads. |

## September 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.237-g6a0f948-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.237-g6a0f948-aliyun|September 16, 2020|Reduces the time that is required to bind ENIs to pods.|This upgrade does not affect your workloads. |

## August 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.221-g8d6386a-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.221-g8d6386a-aliyun|August 11, 2020|Supports IPVLAN and extended Berkeley Packet Filter \(eBPF\) for network virtualization. This feature is available to only users in the whitelist. If you are not included in the whitelist, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).|This upgrade does not affect your workloads. |
|v1.0.10.213-g27145cc-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.213-g27145cc-aliyun|August 4, 2020|Fixes the issue that a pod cannot connect to the network due to occasional ENI failures.|This upgrade does not affect your workloads. |

## July 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.208-gf3144bf-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.208-gf3144bf-aliyun|July 20, 2020|-   Fixes the issue that policy-based routes for nodes in the One ENI for Multi-Pod mode are leaked.
-   Supports API calls over internal networks.
-   Fixes the issue that IP addresses cannot be assigned to pods when the number of vSwitches reaches the upper limit.
-   Optimizes the error report page that returns CNI errors.

|This upgrade does not affect your workloads. |
|v1.0.10.211-gef088a4-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.211-gef088a4-aliyun|July 24, 2020|Allows you to add tags of cluster IDs to ENIs.|This upgrade does not affect your workloads. |

## April 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.156-g8660a0f-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.156-g8660a0f-aliyun|April 22, 2020|-   Improves the cache efficiency through ENIs.
-   Upgrades Felix to V3.5.8.
-   Allows you to reclaim network resources from pods that are in the Completed or Failed state.

|This upgrade does not affect your workloads. |

## February 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.139-g14a4f84-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.139-g14a4f84-aliyun|February 12, 2020|Fixes the issue that pod creation requests occasionally time out.|This upgrade does not affect your workloads.|

## January 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.133-g001396b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.133-g001396b-aliyun|January 10, 2020|-   Allows you to disable the NetworkPolicy feature.
-   Clusters in One ENI for Multi-Pod mode support IPVLAN for network virtualization.

|This upgrade does not affect your workloads.|

## December 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.122-gd0be015-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.122-gd0be015-aliyun|December 24, 2019|Optimizes the efficiency of IP address allocation in the One ENI for Multi-Pod mode.|This upgrade does not affect your workloads.|

## October 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.100-g92a3fa5-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.100-g92a3fa5-aliyun|October 11, 2019|Fixes the issue that the host node is not ready when a large number of jobs concurrently request resources.|This upgrade does not affect your workloads.|

## August 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.9.20-g35ae000-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.9.20-g35ae000-aliyun|August 23, 2019|Supports Kubernetes 1.14.6.|This upgrade does not affect your workloads.|

## April 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.9.15-g3957085-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.9.15-g3957085-aliyun|April 11, 2019|Fixes the issue that the Terway upgrade occasionally fails.|This upgrade does not affect your workloads.|

## March 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.9.14-ga0346bb-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.9.14-ga0346bb-aliyun|March 28, 2019|-   Fixes the issue that Terway fails to obtain the ENI information when throttling is applied to the meta server.
-   Fixes the issue that the **failed to move veth to host netns: file exists** error is returned when you create a container.
-   Supports periodic scanning to check the status of ENIs. ENIs that are abnormally released will be periodically recycled.
-   Optimizes health checks. A TCP port check is performed instead of HTTP path check.

|This upgrade does not affect your workloads.|

