---
keyword: [scale out, node pool, batch scale-out]
---

# Scale out a node pool

You can use a node pool to manage multiple nodes in a Container Service for Kubernetes \(ACK\) cluster as a group. For example, you can centrally manage the labels and taints of the nodes in a node pool. This topic describes how to scale out a node pool in an ACK cluster.

-   A node pool is created. For more information, see [Create a node pool](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Create a node pool.md).
-   The ACK cluster must run Kubernetes 1.9 or later.

**Note:**

-   By default, you can deploy up to 100 nodes in each cluster. To increase the quota, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).
-   Before you add an existing Elastic Compute Service \(ECS\) instance that is deployed in a virtual private cloud \(VPC\), make sure that an elastic IP address \(EIP\) is assigned to the ECS instance, or a NAT gateway is created for the VPC. You must make sure that the node can access the Internet. Otherwise, you may fail to add the ECS instance.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the cluster name. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.

5.  Find the node pool that you want to scale out and click **Scale Out** in the **Actions** column.

6.  In the dialog box that appears, set the scale-out parameters.

    ![Scale out a node pool](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2835359951/p96553.png)

    For more information about how to set the parameters, see [Expand an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Expand an ACK cluster.md). The following list describes some of the parameters:

    -   **Nodes to Add**: the number of nodes that you want to add to the node pool. You can create up to 100 clusters that belong to the current account. You can add up to 500 nodes at a time.
    -   **ECS Label**: You can add labels to the Elastic Compute Service \(ECS\) instances.
    -   **Node Label**: You can add labels to the nodes in the cluster.
    -   **Taints**: You can add taints to the nodes in the cluster.

        **Note:** If you select **Synchronize Node Labels and Taints**, the specified labels and taints are synchronized to the existing and newly added nodes.

7.  Click **Submit**.

    On the Node Pools page, the **state** of the node pool is **scaling** This indicates that the scale-out event is in progress. After the scale-out event is complete, the **state** of the node pool changes to **Active**.


## What to do next

On the Node pools page, find the node pool and click **Details** in the Actions column. On the **Nodes** tab, you can view information about the nodes that are added to the node pool.

