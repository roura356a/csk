---
keyword: [elastic node pool, external Kubernetes cluster]
---

# Create an elastic node pool for an external Kubernetes cluster

You can use an elastic node pool to manage a set of Elastic Compute Service \(ECS\) instances and add these instances to external Kubernetes clusters. This topic describes how to create an elastic node pool for an external Kubernetes cluster.

A cluster for registration is created and registered with an external Kubernetes cluster \(self-managed or provided by other cloud providers\). For more information about how to create a cluster for registration and limits on elastic node pools in registered external Kubernetes clusters, see [Create a cluster registration proxy and register an on-premises cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of registered clusters/Create a cluster registration proxy and register an on-premises cluster.md) and [Limits on elastic node pools for external Kubernetes clusters](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Limits on elastic node pools for external Kubernetes clusters.md).

## Procedure

**Note:**

-   By default, you can deploy up to 100 nodes in a cluster of Container Service for Kubernetes \(ACK\). To add more nodes to a cluster, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).
-   Before you add an existing ECS instance to an ACK cluster, make sure that an elastic IP address \(EIP\) is attached to the ECS instance, or a Network Address Translation \(NAT\) gateway is created in the virtual private cloud \(VPC\) where the ECS instance is deployed. Make sure that the ECS instance can access the Internet. Otherwise, the ECS instance cannot be added to the cluster.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the upper-right corner of the Node Pools page, click **Create Node Pool**.

5.  On the Create Node Pool page, set the parameters for the node pool. For more information about the parameters, see [Create a cluster registration proxy and register an on-premises cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of registered clusters/Create a cluster registration proxy and register an on-premises cluster.md). The following table describes some of the parameters.

    |Parameter|Description|
    |---------|-----------|
    |Quantity|Specify the initial number of nodes in the node pool. If you do not need to create nodes in the node pool, set this parameter to 0.|
    |Operating System|Select the operating system for the nodes. You can select CentOS or Alibaba Cloud Linux 2.1903.|
    |Node Label|You can add labels to the nodes in the node pool.|
    |ECS Label|You can add labels to the selected ECS instances.|

6.  Click **Confirm Order**. Then, click **Submit**. The Node Pools page is as shown in the following figure.

    **Note:** On the Node Pools page, if the **Status** column shows Initializing for the node pool, it indicates that the node pool is being created. After the node pool is created, the **Status** column shows Active for the node pool.


## Related operations

After the node pool is created, go to the Node Pools page, find the created node pool, and then click **Details** in the **Actions** column to view the details about the node pool. You can also click Scale Out in the Actions column to scale out the node pool. For more information about how to scale out a node pool, see [Manually scale out an elastic node pool](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Elastic node pools/Manually scale out an elastic node pool.md).

