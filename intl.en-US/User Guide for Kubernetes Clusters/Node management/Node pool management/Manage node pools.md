---
keyword: [node pool, create a node pool, scale out a node pool]
---

# Manage node pools

Container Service for Kubernetes \(ACK\) allows you to use a node pool to manage multiple nodes in a cluster as a group. For example, you can centrally manage the labels and taints of the nodes in a node pool. This topic describes how to create and scale out a node pool in an ACK cluster.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   The Kubernetes version of the cluster must be later than 1.9.

**Note:**

-   By default, a cluster can contain at most 100 nodes. To increase the quota, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).
-   When you add an Elastic Compute Service \(ECS\) instance to a node pool, make sure that the ECS instance is associated with an elastic IP address \(EIP\) or a NAT gateway is configured for the virtual private cloud \(VPC\) where the ECS instance is deployed. In addition, make sure that the ECS instance can access the Internet. Otherwise, you cannot add the ECS instance.

ACK provides two types of node pool: regular node pools and managed node pools. Regular node pools include default node pools and custom node pools. You can enable auto scaling for custom node pools and managed node pools. For more information, see [Node pool overview](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Node pool overview.md).

## Create a node pool

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.

5.  In the upper-right corner of the Node Pools page, click **Create Node Pool**.

    In the upper-right corner of the Node Pools page, you can also click **Create Managed Node Pool** to create a managed node pool, or click **Configure Auto Scaling** to create an auto-scaling node pool.

6.  In the Create Node Pool dialog box, configure the node pool.

    For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md). The following table describes some of the parameters.

    |Parameter|Description|
    |---------|-----------|
    |**Quantity**|Specify the initial number of nodes in the node pool. If you do not want to create nodes in the node pool, set this parameter to 0.|
    |**Billing Method**|You can select Pay-As-You-Go, Subscription, and Preemptible Instance. For more information, see [Instance purchasing options](/intl.en-US/Instance/Overview.md).|
    |**Auto Scaling**|Select **Enable Auto Scaling** to enable auto scaling of the node pool. For more information, see [Auto scaling of nodes](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Auto scaling of nodes.md).|
    |**Operating System**|Select the node operating system. The CentOS, Alibaba Cloud Linux, and Windows operating systems are supported.|
    |**Public IP**|If you select **Assign a Public IPv4 Address to Each Node**, public IPv4 addresses are assigned to the nodes in the node pool. You can connect to the nodes by using the assigned IP addresses. For more information about public IP addresses, see [Public IP addresses](/intl.en-US/Network/Instance IP addresses/IP addresses of ECS instances within VPCs.md). **Note:** If you select **Assign a Public IPv4 Address to Each Node**, do not select **Associate EIP** when you configure an auto-scaling node pool. Otherwise, nodes cannot be added to the node pool. |
    |**ECS Label**|You can add labels to the ECS instances.|
    |**Node Label**|You can add labels to the nodes in the node pool. **Note:** If you select **Set New Nodes to Unschedulable**, the nodes are unschedulable when they are added to the cluster. You can go to the Nodes page and set the nodes to schedulable. |
    |**Custom Resource Group**|You can specify a resource group for scale-out events. When the node pool is scaled out, nodes from the specified resource group are added to the node pool.|

7.  Click **Confirm Order**.

    On the Node Pools page, if the **state** of the node pool is **initializing**, it indicates that the node pool is being created. After the node pool is created, the **state** of the node pool changes to **active**.

    ![nodepool](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5365359951/p95881.png)


## Scale out a node pool

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.

5.  Find the node pool that you want to scale out and click **Scale Out** in the **Actions** column.

6.  In the dialog box that appears, set the scale-out parameters.

    For more information, see [Create a node pool](#section_eq0_lmv_4a7). The following list describes some of the parameters:

    -   **Nodes to Add**: the number of nodes that you want to add to the node pool. You can add up to 500 nodes at a time.
    -   **ECS Label**: Add labels to the ECS instances.
    -   **Node Label**: Add labels to the nodes to be added to the cluster.

        **Note:**

        -   If you select **Add Labels and Taints to Existing Nodes**, the modifications of labels and taints are synchronized to existing nodes in the node pool. This does not affect the labels and taints that you previously added to the existing nodes.
        -   If you select **Set New Nodes to Unschedulable**, the nodes are unschedulable when they are added to the cluster. You can go to the Nodes page and set the nodes to schedulable.

    -   **Taints**: You can add taints to the nodes to be added to the node pool.
7.  Click **Submit**.

    On the Node Pools page, the **state** of the node pool is **scaling**. This indicates that the scale-out event is in progress. After the scale-out event is completed, the **state** of the node pool changes to **active**.


## Edit a node pool

Find the node pool that you want to edit and click **Edit** in the **Actions** column. For more information about the parameters, see [Create a node pool](#section_eq0_lmv_4a7). The following list describes some of the parameters:

-   **Operating System**: Change the operating system image version for the nodes.
-   **ECS Label**: Add labels to the ECS instances.
-   **Node Label**: Add labels to the nodes in the node pool.

    **Note:**

    -   If you select **Add Labels and Taints to Existing Nodes**, the modifications of labels and taints are synchronized to existing nodes in the node pool. This does not affect the labels and taints that you previously added to the existing nodes.
    -   If you select **Set New Nodes to Unschedulable**, the nodes are unschedulable when they are added to the cluster. You can go to the Nodes page and set the nodes to schedulable.

-   **Taints**: Add taints to the nodes.

## What to do next

On the Node pools page, you can select a node pool and perform the following operations on the node pool:

-   Click **Details** in the **Actions** column to view the details of the node pool.
-   Click the name of the node pool that you want to manage. You can perform the following operations on the details page of the node pool:
    -   Click the **Overview** tab to view information about the cluster, node pool, node configurations, and auto scaling settings.
    -   Click the **Nodes** tab to view information about the nodes in the node pool. You can select multiple nodes and remove them at a time.
    -   Click the **Nodes** tab. In the upper-right corner of the tab, you can click **Export** to export node information as comma-separated values \(CSV\) files.
    -   Click the **Nodes** tab. You can select **Display Only Failed Nodes** to view nodes that failed to be created.
    -   Click the **Scaling Activities** tab to view the latest scaling events of the nodes.

