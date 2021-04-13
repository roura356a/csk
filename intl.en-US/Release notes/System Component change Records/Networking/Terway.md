---
keyword: release notes for Terway
---

# Terway

This topic lists the latest changes to the Terway plug-in.

## February 2021

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.317-g0652857-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.317-g0652857-aliyun|February 22, 2021|-   The issue of identity leakage in Cilium is fixed.
-   The issue that occasionally occurs in the IP management of pods of StatefulSets is fixed.

|No impact on workloads. |
|v1.0.10.309-g5314eee-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.309-g5314eee-aliyun|February 5, 2021|-   Errors in the configurations of security group rules are no longer automatically fixed. The system raises alerts only when errors are detected. You can follow the suggestions to fix the errors.
-   The issue that the IP addresses of pods may be reclaimed and reassigned to the pods when the nodes are overloaded is fixed.

|No impact on workloads. |

## January 2021

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.301-g0115576-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.301-g0115576-aliyun|January 21, 2021|New features:

-   Custom maximum transmission units \(MTUs\) are supported.
-   Routing based on the host network stack in the exclusive ENI mode is supported.

Fixed issues:

-   Packet loss due to traffic throttling of Terway.
-   Residual IP policies.
-   Incorrect counting of the number of IP addresses provided by elastic network interfaces \(ENIs\).

|No impact on workloads. |

## December 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.280-gdc2cb6c-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.280-gdc2cb6c-aliyun|December 25, 2020|-   Proactive checks of Terway are supported. The configurations of the security groups with which ENIs are associated are updated.
-   Routing based on the host network stack when IPVLAN is used is supported.

|No impact on workloads. |
|v1.0.10.263-gdbe50a9-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.263-gdbe50a9-aliyun|December 3, 2020|The error of Terway when the exclusive ENI mode is used is fixed.|No impact on workloads. |

## November 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.261-g8342155-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.261-g8342155-aliyun|November 27, 2020|-   Synchronous calls to the Elastic Compute Service \(ECS\) API are supported.
-   The issue that error messages are returned when the Container Network Interface \(CNI\) plug-in is used is fixed.

|No impact on workloads. |
|v1.0.10.250-gb7bb10a-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.250-gb7bb10a-aliyun|November 23, 2020|-   The issue that a pod cannot connect to the network when the ENI driver fails to be loaded is fixed.
-   The status issue of the IP addresses that are allocated by ENI when the ENI API is throttled is fixed.

|No impact on workloads. |

## October 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.247-g4cb77d0-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.247-g4cb77d0-aliyun|October 26, 2020|ECS instances that are deployed on dedicated hosts are supported.|No impact on workloads. |

## September 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.237-g6a0f948-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.237-g6a0f948-aliyun|September 16, 2020|The time that is required to bind ENIs to pods is reduced.|No impact on workloads. |

## August 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.221-g8d6386a-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.221-g8d6386a-aliyun|August 11, 2020|IPVLAN and the extended Berkeley Packet Filter \(eBPF\) are supported for network virtualization.To use this feature, you must [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to apply for this feature to be enabled on your account.|No impact on workloads. |
|v1.0.10.213-g27145cc-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.213-g27145cc-aliyun|August 4, 2020|The issue that a pod cannot connect to the network due to occasional ENI failures is fixed.|No impact on workloads. |

## July 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.208-gf3144bf-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.208-gf3144bf-aliyun|July 20, 2020|-   The issue that policy-based routes for nodes in the inclusive ENI mode are leaked is fixed.
-   API calls over internal networks are supported.
-   The issue that IP addresses cannot be assigned to pods when the number of vSwitches reaches the upper limit is fixed.
-   The error report page that returns CNI errors is optimized.

|No impact on workloads. |
|v1.0.10.211-gef088a4-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.211-gef088a4-aliyun|July 24, 2020|Adding tags of cluster IDs to ENIs is supported.|No impact on workloads. |

## April 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.156-g8660a0f-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.156-g8660a0f-aliyun|April 22, 2020|-   The cache efficiency is improved when ENIs are used.
-   Felix is upgraded to version 3.5.8.
-   Reclaiming network resources from pods that are in the Completed or Failed state is supported.

|No impact on workloads. |

## February 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.139-g14a4f84-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.139-g14a4f84-aliyun|February 12, 2020|The issue that pod creation requests occasionally time out is fixed.|No impact on workloads.|

## January 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.133-g001396b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.133-g001396b-aliyun|January 10, 2020|-   Disabling the NetworkPolicy feature is supported.
-   IPVLAN is supported for network virtualization in the inclusive ENI mode.

|No impact on workloads.|

## December 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.122-gd0be015-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.122-gd0be015-aliyun|December 24, 2019|The efficiency of IP address allocation is optimized in the inclusive ENI mode.|No impact on workloads.|

## October 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.100-g92a3fa5-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.100-g92a3fa5-aliyun|October 11, 2019|The issue that the host node is not ready when a large number of jobs concurrently request resources is fixed.|No impact on workloads.|

## August 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.9.20-g35ae000-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.9.20-g35ae000-aliyun|August 23, 2019|Kubernetes 1.14.6 is supported.|No impact on workloads.|

## April 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.9.15-g3957085-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.9.15-g3957085-aliyun|April 11, 2019|The issue that the Terway upgrade occasionally fails is fixed.|No impact on workloads.|

## March 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.9.14-ga0346bb-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.9.14-ga0346bb-aliyun|March 28, 2019|-   The issue that Terway fails to obtain the ENI information when throttling is applied to the meta server is fixed.
-   The issue that the **failed to move veth to host netns: file exists** error is returned when you create a container is fixed.
-   Periodic scanning is supported to check the status of ENIs. ENIs that are abnormally released are periodically recycled.
-   Health checks are optimized. TCP port checks are performed instead of HTTP path checks.

|No impact on workloads.|

