---
keyword: [create a node pool, scale out, registered cluster]
---

# Create and scale out a node pool

You can use an elastic node pool to manage a set of Elastic Compute Service \(ECS\) instances and add these instances to a registered Kubernetes cluster. This topic describes how to create an elastic node pool for a registered Kubernetes cluster.

The operations before [Step 6](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of external clusters/Create a hybrid cluster.md) in the Create a hybrid cluster topic are completed.

## Step 1: Create a node pool

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.

5.  In the upper-right corner of the Node Pools page, click **Create Node Pool**.

6.  In the Create Node Pool dialog box, set the parameters for the node pool.

    The following table describes the parameters.

    |Parameter|Description|
    |---------|-----------|
    |**Quantity**|Specify the initial number of nodes in the node pool. If you do not want to create nodes in the node pool, set this parameter to 0.|
    |**Operating System**|Select the operating system for the nodes in the node pool. Only CentOS and Alibaba Cloud Linux 2.1903 are supported.|
    |**Node Label**|You can add labels to the nodes in the node pool.|
    |**ECS Label**|You can add labels to the selected ECS instances.|
    |**Taints**|You can add taints to the nodes in the node pool.|
    |**Security Group**|Select the security group to which the nodes belong.|

    For more information, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).

7.  Click **Confirm Order**.

    On the Node Pools page, if the **state** of the node pool is **Initializing**, it indicates that the node pool is being created. After the node pool is created, the **state** of the node pool changes to **Active**.

    ![Create a node pool](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/1901766161/p248314.png)


## Step 2: Scale out the node pool

Before you scale out the node pool for a registered cluster, take note of the following limits:

-   By default, a cluster can contain at most 100 nodes. To increase the quota, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).
-   When you add an ECS instance to a node pool, make sure that the ECS instance is associated with an elastic IP address \(EIP\) or a NAT gateway is configured for the virtual private cloud \(VPC\) where the ECS instance is deployed. In addition, make sure that the ECS instance has Internet access. Otherwise, you cannot add the ECS instance.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.

5.  On the Node Pools page, find the node pool that you want to scale out and click **Scale Out** in the **Actions** column.

6.  In the dialog box that appears, set **Nodes to Add** to the number of nodes that you want to add and click **Submit**.

    **Note:** If you want to modify the configurations of the node pool, click **Modify Node Pool Settings**.

    On the Node Pools page, the **state** of the node pool changes to **Scaling**. After the node pool is scaled out, the **state** of the node pool changes to **Active**.


## What to do next

1.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.

2.  On the Node Pools page, find the scaled node pool and click **Details** in the Actions column.

3.  Click the **Nodes** tab to view detailed information about the newly added nodes.


