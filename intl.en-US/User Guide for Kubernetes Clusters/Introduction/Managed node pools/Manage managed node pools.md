---
keyword: [managed node pool, create managed node pool, auto upgrading of node, auto repairing of node]
---

# Manage managed node pools

Managed node pools support auto upgrading and auto repairing. This provides centralized, managed, and operations and maintenance \(O&M\)-free lifecycle management on nodes. This topic describes how to create, scale out, clone, and delete a managed node pool.

-   Managed node pools are available for only professional managed Kubernetes cluster and only users in the whitelist. If you are not in the whitelist, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).
-   A professional managed Kubernetes cluster is created. For more information, see [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Introduction/Create a professional managed Kubernetes cluster.md).
-   The ACK cluster must run Kubernetes 1.9 or later.

## Prerequisites

-   Managed node pools perform upgrades on nodes by replacing the system disks of nodes. This deletes the data on the previous system disks. Data disks are not affected. Do not use system disks to persist data.
-   Before the system disk of a node is replaced, ACK disables and drains the node. This may restart pods and interrupt persistent connections.
-   When exceptions occur on a node in a managed node pool, ACK may restart the node to fix the exceptions. This restarts pods on the node.

## Create a managed node pool

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node**.

5.  On the Node Pools page, click **Create Managed Node Pool**.

6.  In the Create Managed Node Pool dialog box, configure the managed node pool.

    For more information about the parameters, see [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Introduction/Create a professional managed Kubernetes cluster.md). The following list describes some of the parameters:

    -   Maintenance Window: Set the time window for auto upgrading on the managed node pool. Click **Set** to go to the **Basic Information** tab of the details page of the cluster. In the Cluster Maintenance section, you can turn on **Maintenance Window** and modify the time window.
7.  Click **Confirm Order**.

    On the Node Pools page, if the **Status** column of the node pool shows **Initializing** for the node pool, it indicates that the node pool is being created. After the managed node pool is created, the **Status** column shows **Active** for the managed node pool.


## Scale out a node pool

You can scale out a managed node pool by adding nodes with attributes that are the sam as those of the existing nodes.

1.  On the Node Pools page, find the node pool that you want to scale out and click **Scale Out** in the **Actions** column.

2.  In the dialog box that appears, set **Nodes to Add**. You can select or enter the number of nodes to be added. Click **Submit**.

    **Note:** If you want to modify the configurations of the node pool, click **Modify Node Pool Settings**. For more information, see [Create a managed node pool](#section_s0g_nbe_bai).

    On the Node Pools page, the **Status** column shows **Scaling** for the node pool. After the node pool is scaled, the **Status** column shows **Active** for the node pool.


## Clone a node pool

You can create a node pool by cloning an existing node pool. The new node pool has the same configurations as the cloned one.

1.  On the Node Pools page, find the node pool that you want to clone and click **Clone** in the **Actions** column.

2.  In the dialog box that appears, enter a name for the new node pool, set the parameters, and click **Submit**.

    On the Node Pools page, the **Status** column shows **Initializing** for the new node pool. After the cloning process is completed, the **Status** column shows **Active** for the new node pool.


## Delete a node pool

To avoid service disruption caused by user errors, we recommend that you remove nodes from a node pool before you delete the node pool.

1.  On the Node Pools page, find and click the node pool that you want to delete.

2.  In the **Node Configurations** section, select all nodes on the **Nodes** tab and click **Remove Node**.

3.  In the **Remove Node** dialog box, select the **Release ECS Instance** and **Drain the Node** check boxes, and click **OK**.

    After the node is removed, the ECS instance is released. This is the ECS instance where the node is deployed.

    -   **Release ECS Instance**:
        -   Select this option to release only pay-as-you-go ECS instances.
        -   Subscription ECS instances are automatically released after the subscription expires.
        -   If you do not select the **Release ECS Instance** check box, the system continues to bill the ECS instance where the node is deployed.
    -   **Drain the Node**: Select this option to migrate pods that run on the removed nodes to other nodes in the cluster. If you select this option, make sure that the other nodes in the cluster have sufficient resources for these pods.

        You can also run the `kubectl drain node-name` command to migrate pods that run on removed nodes to other nodes in the cluster.

        **Note:** The value of node-name must be in the format of your-region-name.node-id. For example, the value can be cn-hangzhou.i-xxx.

        -   your-region-name specifies the region where the cluster is deployed.
        -   node-id specifies the ID of the ECS instance where the node is deployed.
4.  Go to the Node Pools page, find the node pool that you want to delete and click **Delete** in the **Actions** column.

5.  In the **Delete Node Pool** message, click **Confirm**.


