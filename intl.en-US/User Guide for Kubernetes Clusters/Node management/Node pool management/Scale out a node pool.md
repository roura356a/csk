---
keyword: [scale out, node pool, batch scale-out]
---

# Scale out a node pool

Container Service for Kubernetes \(ACK\) provides node pools for centralized management of nodes. For example, you can centrally manage the labels and taints that are added to the nodes in a node pool. This topic describes how to scale out a node pool in the ACK console.

-   A node pool is created. For more information, see [Create a node pool](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Create a node pool.md).
-   The ACK cluster must run Kubernetes 1.9 or later.

**Note:**

-   By default, you can deploy up to 100 nodes in each cluster. To increase the quota, click [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to submit a ticket.
-   Before you add an existing ECS instance that is deployed in a Virtual Private Cloud \(VPC\) network, make sure that an elastic IP address is assigned to the ECS instance, or a Network Address Translation \(NAT\) gateway is created in the same VPC network for the ECS instance. In addition, you must make sure that the node has access to the Internet. Otherwise, you may fail to add the ECS instance.

## Procedure

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the cluster name. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Node Pools**.

5.  Find the node pool that you want to scale out and click **Scale Out** in the **Actions** column.

6.  In the dialog box that appears, set the scale-out parameters.

    ![Scale out a node pool](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2835359951/p96553.png)

    For more information about how to set the parameters, see [Expand a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Expand a cluster.md). The following list describes some of the parameters:

    -   **Nodes to Add**: the number of nodes that you want to add to the node pool. You can add up to 100 nodes to a cluster under the current account. You can add add up to 500 nodes at a time.
    -   **ECS Label**: You can add labels to the Elastic Compute Service \(ECS\) instances.
    -   **Node Label**: You can add labels to the nodes of the cluster.
    -   **Taints**: You can add taints to the nodes of the cluster.

        **Note:** If you select **Synchronize Node Labels and Taints**, you can synchronize the specified labels and taints to the existing and added nodes.

7.  Click **Submit**.

    On the Node Pools page, if the **Status** column shows **Scaling** for the node pool, it indicates that the node pool is being scaled out. After the node pool is scaled out, the **Status** column shows **Active**.


Click **Details** in the Actions column. On the **Nodes** tab, you can view information about the nodes that are added to the node pool.

