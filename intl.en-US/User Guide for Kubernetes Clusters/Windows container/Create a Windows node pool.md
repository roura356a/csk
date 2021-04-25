---
keyword: [Windows node pool, create a Windows node pool]
---

# Create a Windows node pool

You can use a node pool to manage multiple nodes in a Container Service for Kubernetes \(ACK\) cluster as a group. For example, you can centrally manage the labels and taints of the nodes in a node pool. This topic describes how to create Windows nodes by using the node pool feature of ACK.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   The Kubernetes version of the cluster must be later than V1.9.

**Note:**

-   A Windows node pool supports only Flannel as the network plug-in and does not support Terway.

-   By default, you can deploy up to 100 nodes in an ACK cluster. To increase the quota, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).
-   Before you add an existing Elastic Compute Service \(ECS\) instance that is deployed in a virtual private cloud \(VPC\), make sure that an elastic IP address \(EIP\) is associated with the ECS instance, or a NAT gateway is created for the VPC. In addition, you must make sure that the node can access the Internet. Otherwise, you may fail to add the ECS instance.

-   A Windows node pool supports the following operating systems: Windows Server 2019 and Windows Server Core, 1909.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, click **Node Pools**.

5.  In the upper-right corner of the Node Pools page, click **Create Node Pool**.

    In the upper-right corner of the Node Pools page, you can also click **Create Managed Node Pool** to create a managed node pool, or click **Configure Auto Scaling** to create an auto-scaling node pool.

6.  In the Create Node Pool dialog box, configure the node pool.

    ![Parameter](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5365359951/p95988.png)

    For more information about the parameters, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md). The following list describes some of the parameters:

    -   Quantity: Specify the initial number of nodes in the node pool. If you do not want to add nodes to the pool, set this parameter to 0.
    -   Operating System: Select a Windows operating system.
    -   Node Label: Add labels to the nodes in the node pool.
    -   ECS Label: Add labels to the Elastic Compute Service \(ECS\) instances.
7.  Click **Confirm Order**.

    On the Node Pools page, if the **state** of the node pool is **Initializing**, it indicates that the node pool is being created. After the node pool is created, the **state** of the node pool changes to **Active**.

    ![nodepool](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5365359951/p95881.png)


