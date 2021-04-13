# Manually scale out an elastic node pool

You can use elastic node pools that are provided by Container Service for Kubernetes \(ACK\) to manage a set of Elastic Compute Service \(ECS\) instances for external Kubernetes clusters. You can add ECS instance-based nodes from elastic node pools to self-managed Kubernetes clusters or Kubernetes clusters that are provided by other cloud service providers. You can also use node pools to manage the labels and taints of nodes in node pools. If the computing resources are insufficient in an external Kubernetes cluster, you can add computing resources to the elastic node pool that is used by the cluster. This topic describes how to manually scale out an elastic node pool.

[Create an elastic node pool for an external Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Create an elastic node pool for an external Kubernetes cluster.md)

## Prerequisites

Before you scale out an elastic node pool for an external Kubernetes cluster, take note of the following limits:

-   By default, you can deploy up to 100 nodes in an ACK cluster. To increase the quota, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).
-   Before you add an existing ECS instance to an ACK cluster, make sure that an elastic IP address \(EIP\) is assigned to the ECS instance, or a Network Address Translation \(NAT\) gateway is created in the virtual private cloud \(VPC\) where the ECS instance is deployed. This ensures that the ECS instance can access the Internet. Otherwise, the ECS instance cannot be added to the cluster.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  Find the node pool that you want to scale out, click **Scale Out** in the **Actions** column.

5.  In the dialog box that appears, set **Nodes to Add** to the number of nodes that you want to add and click **Submit**.

    **Note:** If you want to modify the configurations of the node pool, click **Modify Node Pool Settings**. For more information, see [Create an elastic node pool for an external Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Create an elastic node pool for an external Kubernetes cluster.md).

    On the Node Pools page, the **Status** column that the initial state of the node pool is **Scaling**. After the node pool is scaled out, the **Status** column shows that the state of the node pool is **Active**.


## View details about the scaled node pool

1.  On the Node Pools page, find the scaled node pool and click **Details** in the Actions column.

2.  Click the **Nodes** tab at the bottom of the page to view detailed information about the newly added nodes.


