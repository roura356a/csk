---
keyword: [node pools, node pool types, overview]
---

# Overview

Container Service for Kubernetes \(ACK\) provides node pools for you to manage nodes in ACK clusters efficiently. Nodes in node pools are easy to manage and maintain. You can configure auto scaling for node pools, manage nodes in batches, and schedule specified nodes in a node pool. Node pools facilitate how you can manage and maintain nodes in an ACK cluster.

The following content describes the terms related to node pools:

-   **Scaling group**: the core of auto scaling. After you configure instance configuration sources for a scaling group and enable the scaling group, the number of Elastic Compute Service \(ECS\) instances in the scaling group can be automatically scaled based on scaling rules. You can also manually add existing ECS instances to the scaling group. For more information, see [Overview](/intl.en-US/Scaling Group/Scaling group/Overview.md).
-   **Replace system disk**: You can initialize a node by replacing the system disk of the node. After the system disk is replaced, the Infrastructure as a Service \(IaaS\) attributes of the node remain unchanged. For example, the node name, instance ID, and IP address remain unchanged. However, the data on the system disk is deleted when the node is reinitialized. The data disks that are mounted to the node are not affected.

    **Note:** Do not use system disks to persist data. We recommend that you use data disks to persist data.

-   **In-place upgrade**: You can directly replace components on a node as required. This is an alternative to replacing the system disk of a node. In-place upgrades do not replace system disks, reinitialize nodes, or destroy the data on nodes.

## Overview

A node pool contains one or more nodes in a cluster. The nodes in a node pool use the same configurations. A default node pool is automatically created based on the number of nodes and configurations that you specify when you create a cluster. You can add node pools of different sizes and types to the cluster. You can create and upgrade node pools. This does not affect the cluster.

In most cases, the nodes in a node pool all have the following attributes:

-   The type and version of the operating system.
-   The type and version of the container runtime.
-   The billing method. Pay-as-you-go and subscription are supported. Elastic node pools also support the pay-by-preemptible-instance billing method.
-   The node specification.
-   The CPU scheduling policy.
-   The labels and taints.
-   The user-defined data.

![nodepool](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0780981161/p203661.png)

## How to work with node pools

Before you work with node pools, take note of the following items:

-   You can create multiple node pools in a cluster. Custom node pools, elastic node pools, and managed node pools are supported.
-   Each cluster contains a default node pool. A default node pool has the same definition as a custom node pool and uses the scalable resources that are created for the cluster. If you call API operations to expand a cluster or add ECS instances without specifying a node pool, the nodes or instances that you add belong to the default node. For more information about how to call API operations to expand a cluster and add existing ECS instances to a cluster, see [Add worker nodes to a cluster](/intl.en-US/API Reference/Clusters/Expand a cluster/Add worker nodes to a cluster.md) and [Add existing ECS instances to a cluster](/intl.en-US/API Reference/Nodes/Add existing ECS instances to a cluster.md).
-   After you create node pools, you can deploy and manage different types of nodes in a cluster.
    -   You can create multiple node pools in a cluster and add nodes that run different types of operating systems to these node pools. The supported operating systems are CentOS, Windows, and AliyunLinux.
    -   You can create node pools of different container runtimes in a cluster. Sandboxed-Container and Docker are supported.
    -   You can create node pools that adopt different billing methods in a cluster. Supported billing methods are pay-as-you-go, subscription, and pay-by-preemptible-instance.
    -   You can configure different scaling rules for node pools in a cluster.
-   Nodes in different node pools can use different configurations.
    -   Resource-related configurations: node specifications, vSwitches, system disks, data disks, security groups, and logon types.
    -   Node-related configurations: user-defined data, CPU scheduling policies, labels, and taints.

## Limits

-   You can create node pools only in clusters of Kubernetes 1.12.6-aliyun.1 or later.

-   By default, you can create up to 20 node pools in each ACK cluster and each node pool can contain up to 1,000 nodes. To increase the quota, [Navigate to the Quota Center page to submit a ticket](https://quotas.console.aliyun.com/products/csk/quotas).


## Billing

Each node pool corresponds to a scaling group. No additional fees are charged for using node pools. For more information about the billing of scaling groups, see [Billing](/intl.en-US/Pricing/Billing.md).

## Types of node pools

ACK supports custom node pools, default node pools, elastic node pools, and managed node pools.



## Comparison among different types of node pools

|Item|Default node pool|Custom node pool|Elastic node pool|Managed node pool|
|----|-----------------|----------------|-----------------|-----------------|
|Scaling method|Manual scaling.|Manual scaling.|Auto scaling.**Note:** Manual scaling is not supported.

|Manual scaling.|
|Whether the node pools can be deleted|No.|Yes.**Note:** You must remove all nodes from a custom node pool before you can delete the custom node pool.

|Yes.|Yes.**Note:** You must remove all nodes from a managed node pool before you delete the managed node pool. |
|Billing method|Pay-as-you-go and subscription.|Pay-as-you-go and subscription.|Pay-as-you-go and pay-by-preemptible-instance.|Pay-as-you-go and subscription.|
|Node upgrading|-   Only manual upgrading is supported.
-   Only kubelet versions can be upgraded.
-   Only in-place upgrades are supported.

|-   Only manual upgrading is supported.
-   Only kubelet versions can be upgraded.
-   Only in-place upgrades are supported.

|-   Only manual upgrading is supported.
-   Only kubelet versions can be upgraded.
-   Only in-place upgrades are supported.

|-   Auto upgrading within a specified maintenance window is supported.
-   Manual upgrading is supported.
-   Allows you to upgrade the operating system, Docker version, and Kubelet version of nodes. You can also upgrade image versions to fix new CVE vulnerabilities.
-   Upgrades are triggered by replacing system disks. |
|Logon type|Supports both key pairs and passwords.|Supports both key pairs and passwords.|Supports both key pairs and passwords.|Supports only key pairs. Logons with passwords are not supported.|
|Auto repairing|Not supported.|Not supported.|Not supported.|Supported.|

**Related topics**  


[Create a node pool](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Create a node pool.md)

[Overview](/intl.en-US/User Guide for Kubernetes Clusters/Introduction/Managed node pools/Overview.md)

[Auto scaling of nodes](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Auto scaling of nodes.md)

