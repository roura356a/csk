---
keyword: [node pool that runs Windows, create a node pool that runs Windows]
---

# Create a node pool that runs Windows

Container Service for Kubernetes \(ACK\) enables centralized management of nodes in a node pool. For example, you can centrally manage the labels and taints that are attached to nodes in a node pool. This topic describes how to create a pool of nodes that runs Windows in the ACK console.

-   An ACK cluster is created. For more information, see [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md).
-   The ACK cluster must run Kubernetes 1.9 or later.

**Note:**

-   By default, you can deploy up to 100 nodes in each cluster. To increase the quota, click [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to submit a ticket.
-   Before you add an existing ECS instance that is deployed in a Virtual Private Cloud \(VPC\) network, make sure that an elastic IP address is assigned to the ECS instance, or a Network Address Translation \(NAT\) gateway is created in the same VPC network for the ECS instance. In addition, you must make sure that the node has access to the Internet. Otherwise, you may fail to add the ECS instance.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Node Pools**.

5.  On the Node Pools page, click **Create Node Pool**.

6.  In the Create Node Pool dialog box, configure the node pool.

    ![Parameters](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5365359951/p95988.png)

    For more information about the parameters, see [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md). The following list describes some of the parameters:

    -   Quantity: Specify the number of nodes that you want to add to the node pool. If you do not want to add nodes to the pool, set this parameter to 0.
    -   Operating System: Select Windows.
    -   Node Label: Attach labels to nodes in the node pool.
    -   ECS Label: Attach labels to the Elastic Compute Service \(ECS\) instances.
7.  Click **OK**.

    On the Node Pools page, the **Status** column of the node pool displays **Initializing**. This indicates that the node pool creation is in progress. After the node pool is created, the **Status** column displays **Active**.

    ![nodepool](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5365359951/p95881.png)


After the node pool is created, go to the Node Pools page, find the created node pool, and then click **Details** in the **Actions** column to view the details of the node pool. You can also click Scale Out in the Actions column to scale out the node pool.

