# Overview

Managed node pools are provided by Container Service for Kubernetes \(ACK\). Managed node pools support auto upgrading and auto repairing. This provides centralized, managed, and operations and maintenance \(O&M\)-free lifecycle management on nodes. You do not need to be concerned about the O&M of cluster nodes, such as component upgrading, OS upgrading, and patching to fix Common Vulnerabilities and Exposures \(CVE\)-identified vulnerabilities. ACK automatically fixes node exceptions on nodes in a managed node pool.

The following list describes the terms that are related to node pools:

-   **Basic node pool**: You can use a basic node pool to manage a set of nodes that have the same configurations, such as specifications, labels, and taints. You can manually perform O&M operations on nodes in a basic node pool.
-   **Managed node pool**: Managed node pools are based on basic node pools and provide additional features, such as auto upgrading and auto repairing on nodes. This provides automated and managed O&M on nodes.
-   **Replace system disk**: You can initialize a node by replacing the system disk of the node. After the system disk is replaced, the attributes of the Infrastructure as a Service \(IaaS\) services that are attached to the node remain unchanged, such as the node name, the related instance ID, and the assigned IP addresses. However, the original data on the system disk is deleted. Then, the node is initialized again. Attached data disks are not affected.

    **Note:** Do not use system disks to persist data. We recommend that you use data disks to persist data.

-   **In-place upgrade**: You can directly replace components as required on the system disk of a node. This is an alternative to replacing the system disk of a node. In-place upgrades do not replace system disks, reinitialize nodes, or destroy the original data on nodes.

## Scenarios

-   Users focus on application development instead of the O&M of worker nodes.
-   Users require quick patching to fix CVE-identified vulnerabilities. When a vulnerability is disclosed by CVE, upgrades are automatically performed on nodes to fix the vulnerability.
-   Users require elasticity instead of immutability for workloads. The business pods of these users are insensitive to node changes and tolerable to migrations.
-   Users require automatic upgrading of the Docker versions and operating system \(OS\) image versions on nodes.

## Comparison between managed node pools and basic node pools

|Item|Managed node pool|Basic node pool|
|----|-----------------|---------------|
|O&M|Managed by ACK|Managed by users|
|Node upgrading|-   Auto upgrading during maintenance windows.
-   Manual upgrading is also supported to fix CVE-identified vulnerabilities or upgrade the OS versions, Docker versions, and kubelet versions.
-   Upgrading is implemented by replacing system disks.

|-   Only manual upgrading is supported.
-   Only kubelet versions can be upgraded.
-   Upgrading is implemented by in-place upgrades. |
|Auto repairing|Supported|Not supported|
|Key management|Only key pairs are supported.|Key pairs and passwords are supported.|

## Features

-   You can create multiple managed node pools in an ACK cluster. The configuration of each managed node pool can be different from the others. This allows you to manage multiple sets of nodes with different specifications for your cluster.
-   Before a node is upgraded by replacing the system disk of the node, ACK runs the kubectl cordon command to change the node to the Unschedulable state. Then, pods on the node are evicted. If pods are not evicted within 15 minutes, ACK forcibly replaces the system disk.
-   A managed node pool monitors the state of nodes in the node pool. If the state is not reported from a node for more than 10 minutes or a node is in the NotReady state, ACK restarts the node to restore the workloads on the node.
-   You can also disable the auto upgrading feature for a managed node pool. After auto upgrading is disabled, ACK does not automatically upgrade the Docker versions or OS versions when later versions are released.
-   You must set maintenance windows for auto upgrading on nodes. ACK performs upgrades during maintenance windows. If an upgrade is not completed at the end of a maintenance window, it is suspended and resumed at the beginning of the next maintenance window.

## Prerequisites

-   Managed node pools perform upgrades on nodes by replacing the system disks of nodes. This deletes the data on the previous system disks. Data disks are not affected. Do not use system disks to persist data.
-   Before the system disk of a node is replaced, ACK disables and drains the node. This may restart pods and interrupt persistent connections.
-   When exceptions occur on a node in a managed node pool, ACK may restart the node to fix the exceptions. This restarts pods on the node.

