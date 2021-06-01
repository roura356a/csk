---
keyword: [Terway release notes, terway-eniip]
---

# Terway

Terway is an open source Container Network Interface \(CNI\) plug-in developed by Alibaba Cloud. Terway works with Virtual Private Cloud \(VPC\) and allows you to use standard Kubernetes network policies to regulate how containers communicate with each other. You can use Terway to enable internal communications within a Kubernetes cluster. This topic lists the latest changes to the Terway plug-in.

## Overview

Terway is a CNI plug-in developed by Container Service for Kubernetes \(ACK\). The plug-in builds networks based on Elastic Network Interface \(ENI\) to make full use of cloud resources. Terway supports the use of Extended Berkeley Packet Filter \(eBPF\) to accelerate network traffic and reduce latency. It supports standard Kubernetes network policies that define how containers communicate with each other and provides compatibility with Calico network policies.

In a cluster that has Terway installed, each pod has a separate network stack and is assigned a separate IP address. Pods on the same Elastic Compute Service \(ECS\) instance communicate with each other by forwarding packets inside the ECS instance. Pods on different ECS instances communicate with each other through ENIs in the VPC where the ECS instances are deployed. This improves communication efficiency because no tunneling technologies such as Virtual Extensible Local Area Network \(VXLAN\) are required to encapsulate packets.

## Instruction

For more information about how to use Terway, see [Work with Terway](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Work with Terway.md).

## Release notes

**May 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.368-g2890967-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.368-g2890967-aliyun|2021-05-24|-   The issue that the `ResourceInvalid` alert occurs when elastic IP addresses \(EIPs\) are used is fixed.
-   Communication between pods on a node is supported in IPVLAN mode. Data can be transmitted within the node and does not need to be transmitted by using the virtual private cloud \(VPC\) where the node is deployed.
-   Proactive checks and corrections of the `ip forwarding` settings are supported.

|No impact on workloads. |

**April 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.333-gfd2b7b8-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.333-gfd2b7b8-aliyun|2021-04-26|-   The IP address conflicts that occur when stateful applications use EIPs are fixed.
-   Hubble can be enabled in IPVLAN mode. For more information about Hubble, see [What is Hubble](https://github.com/cilium/hubble#what-is-hubble).

|No impact on workloads. |

**March 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.323-g778c128-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.323-g778c128-aliyun|2021-03-22|-   The number of calls to API operations is reduced.
-   The event alerts are optimized when security group configurations are inspected.

|No impact on workloads. |

**February 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.317-g0652857-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.317-g0652857-aliyun|2021-02-22|-   The issue of identity leakage in Cilium is fixed.
-   The issue that occasionally occurs in the IP management of StatefulSet pods is fixed.

|No impact on workloads. |
|v1.0.10.309-g5314eee-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.309-g5314eee-aliyun|2021-02-05|-   Errors in the configurations of security group rules are no longer automatically fixed. The system raises alerts only when errors are detected. You can follow the suggestions to fix the errors.
-   The issue that the IP addresses of pods may be reclaimed and reassigned to the pods when the nodes are overloaded is fixed.

|No impact on workloads. |

**January 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.301-g0115576-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.301-g0115576-aliyun|2021-01-21|New features:

-   Custom maximum transmission units \(MTUs\) are supported.
-   Routing based on the host network stack in the exclusive ENI mode is supported.

Fixed issues:

-   Packet loss due to traffic throttling of Terway.
-   Residual IP policies.
-   Incorrect counting of the number of IP addresses provided by ENIs.

|No impact on workloads. |

**December 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.280-gdc2cb6c-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.280-gdc2cb6c-aliyun|2020-12-25|-   Proactive checks of Terway are supported. The following items are checked:
    -   ENIs and secondary IP addresses of ENIs.
    -   The consistency of security groups.
    -   The pod network configurations.
    -   The host network configurations.
-   Routing based on the host network stack when IPVLAN is used is supported.

|No impact on workloads. |
|v1.0.10.263-gdbe50a9-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.263-gdbe50a9-aliyun|2020-12-03|The error in Terway when the exclusive ENI mode is used is fixed.|No impact on workloads. |

**November 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.261-g8342155-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.261-g8342155-aliyun|2020-11-27|-   Synchronous calls to the ECS API are supported.
-   The issue that error messages are returned when the CNI plug-in is used is fixed.

|No impact on workloads. |
|v1.0.10.250-gb7bb10a-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.250-gb7bb10a-aliyun|2020-11-23|-   The issue that a pod cannot connect to the network when the ENI driver fails to be loaded is fixed.
-   The status issue of the IP addresses that are allocated by ENI when the ENI API is throttled is fixed.

|No impact on workloads. |

**October 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.247-g4cb77d0-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.247-g4cb77d0-aliyun|2020-10-26|ECS instances that are deployed on dedicated hosts are supported.|No impact on workloads. |

**September 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.237-g6a0f948-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.237-g6a0f948-aliyun|2020-09-16|The time that is required to bind ENIs to pods is reduced.|No impact on workloads. |

**August 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.221-g8d6386a-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.221-g8d6386a-aliyun|2020-08-11|IPVLAN and the extended Berkeley Packet Filter \(eBPF\) are supported for network virtualization.To use this feature, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to apply for this feature to be enabled on your account.|No impact on workloads. |
|v1.0.10.213-g27145cc-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.213-g27145cc-aliyun|2020-08-04|The issue that a pod cannot connect to the network due to occasional ENI failures is fixed.|No impact on workloads. |

**July 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.208-gf3144bf-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.208-gf3144bf-aliyun|2020-07-20|-   The issue that policy-based routes for nodes in the inclusive ENI mode are leaked is fixed.
-   API calls over internal networks are supported.
-   The issue that IP addresses cannot be assigned to pods when the number of vSwitches reaches the upper limit is fixed.
-   The error report page that returns CNI errors is optimized.

|No impact on workloads. |
|v1.0.10.211-gef088a4-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.211-gef088a4-aliyun|2020-07-24|Adding tags of cluster IDs to ENIs is supported.|No impact on workloads. |

**April 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.156-g8660a0f-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.156-g8660a0f-aliyun|2020-04-22|-   The cache efficiency is improved when ENIs are used.
-   Felix is upgraded to version 3.5.8.
-   Reclaiming network resources from pods that are in the Completed or Failed state is supported.

|No impact on workloads. |

**February 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.139-g14a4f84-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.139-g14a4f84-aliyun|2020-02-12|The issue that pod creation requests occasionally time out is fixed.|No impact on workloads.|

**January 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.133-g001396b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.133-g001396b-aliyun|2020-01-10|-   Disabling the NetworkPolicy feature is supported.
-   IPVLAN is supported for network virtualization in the inclusive ENI mode.

|No impact on workloads.|

**December 2019**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.122-gd0be015-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.122-gd0be015-aliyun|2019-12-24|The efficiency of IP address allocation is optimized in the inclusive ENI mode.|No impact on workloads.|

**October 2019**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.10.100-g92a3fa5-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.10.100-g92a3fa5-aliyun|2019-10-11|The issue that the host node is not ready when a large number of jobs concurrently request resources is fixed.|No impact on workloads.|

**August 2019**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.9.20-g35ae000-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.9.20-g35ae000-aliyun|2019-08-23|Kubernetes 1.14.6 is supported.|No impact on workloads.|

**April 2019**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.9.15-g3957085-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.9.15-g3957085-aliyun|2019-04-11|The issue that the Terway upgrade occasionally fails is fixed.|No impact on workloads.|

**March 2019**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.9.14-ga0346bb-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/terway:v1.0.9.14-ga0346bb-aliyun|2019-03-28|-   The issue that Terway fails to obtain the ENI information when throttling is applied to the meta server is fixed.
-   The issue that the **failed to move veth to host netns: file exists** error is returned when you create a container is fixed.
-   Periodic scanning is supported to check the status of ENIs. ENIs that are abnormally released are periodically recycled.
-   Health checks are optimized. TCP port checks are performed instead of HTTP path checks.

|No impact on workloads.|

**Related topics**  


[Work with Terway](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Work with Terway.md)

