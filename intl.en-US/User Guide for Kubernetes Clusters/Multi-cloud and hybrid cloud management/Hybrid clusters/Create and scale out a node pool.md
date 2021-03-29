---
keyword: [create a node pool, scale out, registered cluster]
---

# Create and scale out a node pool

You can use an elastic node pool to manage a set of Elastic Compute Service \(ECS\) instances and add these instances to a hybrid cluster. This topic describes how to create an elastic node pool for a hybrid cluster.

A cluster registration proxy is created and a self-managed cluster is registered in the Container Service for Kubernetes \(ACK\) console. The self-managed cluster can be deployed in a data center or on a third-party cloud. For more information, see [Create a cluster registration proxy and register an on-premises cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Register an external Kubernetes cluster.md).

## Step 1: Create a node pool

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.

5.  On the Node Pools page, click **Create Node Pool**.

6.  In the Create Node Pool dialog box, set the parameters for the node pool.

    The parameters are described in the following table:

    |Parameter|Description|
    |---------|-----------|
    |**Quantity**|Set the initial number of nodes in the node pool. If you do not want to create nodes in the node pool, set this parameter to 0.|
    |**Operating System**|Select the operating system for the nodes. Valid values: CentOS and Alibaba Cloud Linux 2.1903.|
    |**Node Label**|You can add labels to nodes in the node pool.|
    |**ECS Label**|You can add labels to the ECS instances in the node pool.|
    |**Taints**|You can add taints to nodes in the node pool.|
    |**Security Group**|Select the security group to which the nodes belong.|

    For more information about the parameters, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).

7.  Click **OK**.

    On the Node Pools page, verify that the node pool is in the **Initializing** state. This state indicates that the node pool is being created.**** After the node pool is created, the node pool is in the **Active** state.****

    ![Create a node pool](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1901766161/p248314.png)


## Step 2: Scale out the node pool

Before you scale out the node pool for a cluster, take note of the following limits:

-   By default, a cluster can contain up to 100 nodes. To increase the quota, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).
-   When you add an ECS instance to a node pool, make sure that the ECS instance is associated with an elastic IP addresses \(EIP\) or a NAT gateway is configured for the virtual private cloud \(VPC\) where the ECS instance is deployed. In addition, make sure that the ECS instance has Internet access. Otherwise, you cannot add the ECS instance.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.

5.  On the Node Pools page, find the node pool that you want to scale out and click **Scale Out** in the **Actions** column.


For more information, see [Scale out a node pool](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Scale out a node pool.md).

## Related operations

Select a node pool and click **Details** in the Actions column. On the **Nodes** tab, view the nodes that belong to the node pool.

