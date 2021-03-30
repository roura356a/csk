---
keyword: [managed node pool, create managed node pool, node auto-upgrade, node auto-repair]
---

# Manage managed node pools

Managed node pools support node auto-upgrade and auto-repair. This provides a centralized, managed, and operations and maintenance \(O&M\)-free lifecycle management of nodes. This topic describes how to create, scale out, clone, and delete a managed node pool.

-   A professional Kubernetes cluster is created. For more information, see [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md).
-   The ACK cluster must run Kubernetes 1.9 or later.

## Prerequisites

-   Managed node pools perform upgrades on nodes by replacing the system disks of nodes. This deletes the data on the previous system disks. Data disks are not affected. Do not use system disks to persist data.
-   Before the system disk of a node is replaced, ACK disables and drains the node. This may restart pods and interrupt persistent connections.
-   When exceptions occur on a node in a managed node pool, ACK may restart the node to fix the exceptions. This restarts pods on the node.

## Create a managed node pool

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.

5.  In the upper-right corner of the Node Pools page, click **Create Managed Node Pool**.

6.  In the Create Managed Node Pool dialog box, set the parameters of the managed node pool.

    For more information about the parameters, see [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md). The following list describes some of the parameters:

    -   Maintenance Window: Set the time window for the auto-upgrade of nodes in the managed node pool. Click **Set** to go to the **Basic Information** tab of the details page of the cluster. In the Cluster Maintenance section, you can turn on **Maintenance Window** and modify the time window for maintenance.
    -   Auto Scaling: Specify whether to enable auto scaling of the managed node pool. For more information, see [Auto scaling of nodes](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Auto scaling of nodes.md).
    -   Quantity: Specify the number of nodes that you want to add to the node pool. If you do not want to create nodes, set this parameter to 0.
    -   Operating System: Select an operating system for the nodes. CentOS, Alibaba Cloud Linux, and Windows are supported.
    -   Node Label: You can add labels to nodes.
    -   ECS Label: You can add labels to ECS instances.
    -   Custom Resource Group: You can specify the resource group to which the nodes in the node pool belong.
7.  Click **Confirm Order**.

    On the Node Pools page, check the **Status** column of the managed node pool. If the node pool is in the **Initializing** state, it indicates that the node pool is being created. After the managed node pool is created, the managed node pool is in the **Active****state**.


## Upgrade a managed node pool

If the operating system of nodes in a managed node pool is upgradable, you can upgrade the node pool.

1.  Find the managed node pool that you want to upgrade and click **Upgrade** in the **Actions** column.

2.  In the **Upgrade Node Pool** dialog box, set the following parameters.

    |Parameter|Description|
    |---------|-----------|
    |Required Version|By default, **OS Image** and **Container Runtime** are selected.    -   **OS Image**: After you select this check box, the operating system of all nodes is upgraded to the required version.
    -   **Container Runtime**: After you select this check box, the container runtime of all nodes is upgraded to the required version. |
    |Maximum Unschedulable Nodes|Specifies the maximum number of nodes that can be in the unschedulable state.|
    |Additional Nodes|Specifies the number of additional nodes that are automatically added before the node pool is upgraded. These additional nodes are automatically removed after the node pool is upgraded. Additional nodes are used to host workloads from upgradable nodes when they are drained.|

    **Note:** The sum of the values of **Maximum Unschedulable Nodes** and **Additional Nodes** equals the number of nodes that can be concurrently upgraded.


## Scale out a managed node pool

You can scale out a managed node pool by adding nodes with the same attributes as the existing nodes.

1.  On the Node Pools page, find the node pool that you want to scale out and click **Scale Out** in the **Actions** column.

2.  In the dialog box that appears, set **Nodes to Add**. You can select or enter the number of nodes to be added. Then, click **Submit**.

    **Note:** If you want to modify the configurations of the node pool, click **Modify Node Pool Settings**. For more information, see [Create a managed node pool](#section_s0g_nbe_bai).

    On the Node Pools page, the node pool is in the **Scaling** **state** when it is being scaled. After the scaling process is complete, the **state** of the node pool is changed to **Active**.


## Clone a managed node pool

You can create a node pool by cloning an existing node pool. The new node pool has the same configurations as the existing one.

1.  On the Node Pools page, find the node pool that you want to clone and click **Clone** in the **Actions** column.

2.  In the dialog box that appears, enter a name for the new node pool, set the parameters, and click **Submit**.

    On the Node Pools page, the node pool is in the **Scaling****state** when it is being created. After the cloning process is complete, the **state** of the node pool is changed to **Active**.


## Delete a managed node pool

Before you delete a managed node pool, we recommend that you remove nodes from the managed node pool to avoid service disruptions caused by user errors.

1.  On the Node Pools page, find and click the node pool that you want to delete.

2.  In the **Node Configurations** section, select all nodes on the **Nodes** tab and click **Remove Node**.

3.  In the **Remove Node** dialog box, select **Release ECS Instance** and **Drain the Node**, and then click **OK**.

    -   **Release ECS Instance**:
        -   This option releases only pay-as-you-go ECS instances.
        -   Subscription ECS instances are automatically released after the subscription expires.
        -   If you do not select **Release ECS Instance**, the system continues to bill the ECS instance where the node is deployed.
    -   **Drain the Node**: Select this option to migrate pods that run on the removed nodes to other nodes in the cluster. If you select this option, make sure that the other nodes in the cluster have sufficient resources for these pods.

        You can also run the `kubectl drain node-name` command to migrate pods that run on removed nodes to other nodes in the cluster.

        **Note:** The value of node-name must be in the format of your-region-name.node-id. For example, you can enter cn-hangzhou.i-xxx.

        -   your-region-name specifies the name of the region where the cluster is deployed.
        -   node-id specifies the ID of the ECS instance where the node to be removed is deployed.
4.  Go to the Node Pools page, find the node pool that you want to delete, and click **Delete** in the **Actions** column.

5.  In the **Delete Node Pool** message, click **Confirm**.


## Enable and disable managed node pools

You can enable managed node pools to convert default node pools, custom node pools, and auto-scaling node pools into managed node pools. You can also disable managed node pools. This section describes how to enable and disable managed node pools.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.

5.  Find the node pool that you want to manage and perform the following operations in the **Actions** column.

    -   Click **Enable Managed Node Pool** to convert the node pool into a managed node pool.
    -   Click **Disable Managed Node Pool** to convert the managed node pool into other types of node pools.

        After the managed node pool is disabled, the managed node pool is converted based on the original type of the node pool. If the original type is managed node pool, it is converted into a custom node pool. Otherwise, it is converted into the original type of the node pool. For example, a default node pool can be converted into a managed node pool. Then, if you disable the managed node pool, it is converted into a default node pool.


