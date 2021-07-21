---
keyword: [node pools, node pool types, node pool overview]
---

# Node pool overview

You can create node pools to facilitate how you manage nodes. For example, you can schedule, configure, or maintain nodes by node pool, or enable auto scaling for a node pool. This topic describes what a node pool is and the types, usage notes, and limits of node pools.

## What is a node pool?

A node pool contains one or more nodes that have the same configurations in a cluster. A default node pool is automatically created based on the number of nodes and configurations that you specify when you create a cluster. You can add node pools of different sizes and types to the cluster. You can create and upgrade node pools. This does not affect the cluster.

In most cases, the following configurations are the same for nodes in a node pool:

-   The type and version of the operating system.
-   The type and version of the container runtime.
-   The billing method. Pay-as-you-go and subscription are supported. Elastic node pools also support preemptible instances.
-   The node specification.
-   The CPU scheduling policy.
-   The labels and taints.
-   The user-defined data.

![nodepool](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0780981161/p203661.png)

The following table describes the terms that are related to node pools.

|Term|Description|
|----|-----------|
|**Scaling group**|A scaling group is a logical group of Elastic Compute Service \(ECS\) instances. Scaling groups are used to manage and automatically scale nodes. Node pools manage nodes based on scaling groups. The resources in a node pool, including ECS instances and scaling groups, must belong to the same Alibaba Cloud account. For more information, see [Overview](/intl.en-US/Scaling Group/Scaling group/Overview.md). **Note:** You must use node pools to configure and manage nodes. Do not use scaling groups to configure or manage nodes. Otherwise, some node pool features may become unavailable. |
|**Replace system disk**|You can initialize a node by replacing the system disk of the node. After the system disk is replaced, the Infrastructure as a Service \(IaaS\) attributes of the node remain unchanged. For example, the node name, instance ID, and IP address remain unchanged. However, the data on the system disk is deleted when the node is re-initialized. The data disks that are mounted to the node are not affected. **Note:** Do not use system disks to persist data. We recommend that you use data disks to persist data. |
|**In-place upgrade**|You can directly replace components on a node as required. This is an alternative to replacing the system disk of a node. In-place upgrades do not replace system disks, re-initialize nodes, or destroy the data on nodes.|

## Usage notes

Before you use node pools, take note of the following items:

-   You can create multiple node pools in a cluster. Regular node pools and managed node pools are supported. For more information about node pool types, see [Types](#section_7qb_kzz_vct).
-   A default node pool is automatically created based on the number of nodes and configurations that you specify when you create a cluster. You cannot delete the default node pool. If you call API operations to expand a cluster or add ECS instances without specifying a node pool, the nodes or instances that you add are grouped to the default node pool. For more information about how to call API operations to expand a cluster and add existing ECS instances to a cluster, see [ScaleClusterNodePool](/intl.en-US/API Reference/Node pools/ScaleClusterNodePool.md) and [AttachInstances](/intl.en-US/API Reference/Node pools/AttachInstances.md).

    **Note:** In an existing dedicated cluster that is deployed across three zones, the system creates three default node pools because three scaling groups are created when you create the cluster.

-   After you create node pools, you can deploy and manage different types of node in a cluster.
    -   You can create multiple node pools in a cluster and add nodes that run different operating systems to these node pools. The supported operating systems are CentOS, Windows, and AliyunLinux.
    -   You can create node pools of different container runtimes in a cluster. Sandboxed-Container and Docker are supported.
    -   You can create node pools that use different billing methods in a cluster. Supported billing methods are pay-as-you-go, subscription, and pay-by-preemptible-instance.
    -   You can enable auto scaling for multiple node pools in a cluster.
-   Nodes in different node pools can use different configurations.
    -   Resource-related configurations: node specifications, vSwitches, system disks, data disks, security groups, and logon types.
    -   Node-related configurations: user-defined data, CPU scheduling policies, labels, and taints.

## Limits

-   You can create node pools only in clusters of Kubernetes 1.12.6-aliyun.1 or later.

-   By default, you can create at most 20 node pools in each ACK cluster. To increase the quota, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). Each node pool can contain at most 1,000 nodes.


## Billing

Each node pool corresponds to a scaling group. No fees are charged for node pools. However, you are charged for the cloud resources that are used by node pools, such as ECS instances.

For more information about the billing of scaling groups, see [Billing](/intl.en-US/Pricing/Billing.md).

## Types

ACK provides regular node pools and managed node pools.

|Type|Description|
|----|-----------|
|Regular node pool|A node pool contains one or more nodes that have the same configurations in a cluster. Each node pool corresponds to a scaling group. When you scale a node pool, ACK uses the Auto Scaling service to add or remove nodes. You can create and manage multiple node pools based on your requirements.|
|Managed node pool|Managed node pools support auto upgrade and auto repair. This type of node pool features centralized, managed, and O&M-free lifecycle management of nodes. You do not need to be concerned about the maintenance of cluster nodes, such as component upgrades, OS upgrades, and patching of Common Vulnerabilities and Exposures \(CVE\)-identified vulnerabilities. ACK automatically fixes faulty nodes in a managed node pool. For more information, see [Overview](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Managed node pools/Overview.md). |

## Comparison of node pools

|Item|Regular node pool|Managed node pool|
|----|-----------------|-----------------|
|Manual scaling|![勾](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7998975261/p278633.png)

|![勾](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7998975261/p278633.png) |
|Can be deleted|![勾](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7998975261/p278633.png)

|![勾](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7998975261/p278633.png) |
|Pay-as-you-go and subscription|![勾](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7998975261/p278633.png)

|![勾](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7998975261/p278633.png) |
|Auto repair| |![勾](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7998975261/p278633.png) |
|Enable auto scaling|![勾](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7998975261/p278633.png)

|![勾](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7998975261/p278633.png) |
|Manual upgrade|![勾](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7998975261/p278633.png)

|![勾](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7998975261/p278633.png) |
|Kubelet upgrade|![勾](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7998975261/p278633.png)

|![勾](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7998975261/p278633.png) |
|Docker upgrade and CVE vulnerability patching| |![勾](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7998975261/p278633.png) |
|Auto upgrade during the maintenance window| |![勾](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7998975261/p278633.png) |
|Upgrade by replacing system disks| |![勾](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7998975261/p278633.png) |
|Key pair logon|![勾](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7998975261/p278633.png)

|![勾](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7998975261/p278633.png) |
|Password logon|![勾](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7998975261/p278633.png)

| |

**Note:**

-   You must remove all nodes in a node pool before you can delete the node pool.
-   You can enable auto scaling for a node pool only when you create the node pool. After auto scaling is enabled for a node pool, the node pool has the following features:

    -   Manual scaling is not supported.
    -   The pay-by-preemptible-instance billing method is supported.
    -   Elastic node pools support standard CPU instances, GPU instances, and shared GPU instances.
    -   You can disable auto scaling to convert an elastic node pool to a regular node pool. However, you cannot convert a regular node pool to an elastic node pool.
    For more information, see [Auto scaling of nodes](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Auto scaling of nodes.md).

-   You must use the in-place upgrade method to upgrade node pools other than managed node pools.

**Related topics**  


[Manage a node pool](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Manage a node pool.md)

[Overview](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Managed node pools/Overview.md)

[Auto scaling of nodes](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Auto scaling of nodes.md)

