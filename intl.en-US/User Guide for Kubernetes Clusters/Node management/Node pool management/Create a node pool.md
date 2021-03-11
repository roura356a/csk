---
keyword: [node pool, create a node pool]
---

# Create a node pool

You can add nodes to a node pool. This allows you to manage multiple nodes as a group. For example, you can manage the labels and taints that are added to the nodes in the node pool. This topic describes how to create a node pool in the Container Service for Kubernetes \(ACK\) console.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   The ACK cluster must run Kubernetes 1.9 or later.

**Note:**

-   By default, you can deploy up to 100 nodes in each cluster. To increase the quota, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).
-   Before you add an existing Elastic Compute Service \(ECS\) instance that is deployed in a virtual private cloud \(VPC\), make sure that an elastic IP address \(EIP\) is assigned to the ECS instance, or a NAT gateway is created for the VPC. You must make sure that the node can access the Internet. Otherwise, you may fail to add the ECS instance.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  Go to the Node Pools page.

    -   In the left-side navigation pane, choose **Clusters** \> **Clusters**. On the Clusters page, find the cluster that you want to manage and click **Node Pools** in the **Actions** column for the cluster.
    -   In the left-side navigation pane, choose **Clusters** \> **Node Pools**. On the Node Pools page, select the cluster that you want to manage from the drop-down list in the upper-left corner.
3.  In the left-side navigation pane of the ACK console, click **Clusters**.

4.  On the Clusters page, find the cluster that you want to manage and click its ID.

5.  On the Node Pools page, click **Create Node Pool**.

6.  On the Create Node Pool page, set the parameters for the node pool.

    For more information, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md). The following list describes some of the parameters:

    -   Quantity: Specify the initial number of nodes in the node pool. If you do not want to create nodes, set this parameter to 0.
    -   Operating System: Select an operating system for the nodes. Supported operating systems are CentOS, Alibaba Cloud Linux, and Windows.
    -   Node Label: You can add labels to the nodes.
    -   ECS Label: You can add labels to the ECS instances.
    -   Custom Resource Group: You can specify the resource group to which the nodes in the node pool belong.
7.  Click **Confirm Order**.

    On the Node Pools page, if the **Status** column of the node pool shows **Initializing**, it indicates that the node pool is being created. After the node pool is created, the **Status** column displays **Active**.

    ![nodepool](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5365359951/p95881.png)


After the node pool is created, go to the Node Pools page, find the created node pool, and then click **Details** in the **Actions** column to view the details of the node pool. You can also click Scale Out in the Actions column to scale out the node pool.

