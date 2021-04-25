---
keyword: [node pool, create a node pool]
---

# Create a node pool

You can use a node pool to manage multiple nodes in a Container Service for Kubernetes \(ACK\) cluster as a group. For example, you can centrally manage the labels and taints of the nodes in a node pool. This topic describes how to create a node pool in the ACK console.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   The Kubernetes version of the cluster must be later than V1.9.

**Note:**

-   By default, you can deploy up to 100 nodes in an ACK cluster. To increase the quota, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).
-   Before you add an existing Elastic Compute Service \(ECS\) instance that is deployed in a virtual private cloud \(VPC\), make sure that an elastic IP address \(EIP\) is associated with the ECS instance, or a NAT gateway is created for the VPC. In addition, you must make sure that the node can access the Internet. Otherwise, you may fail to add the ECS instance.

## Create a node pool

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the cluster ID.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.

5.  In the upper-right corner of the Node Pools page, click **Create Node Pool**.

    In the upper-right corner of the Node Pools page, you can also click **Create Managed Node Pool** to create a managed node pool, or click **Configure Auto Scaling** to create an auto-scaling node pool.

6.  In the Create Node Pool dialog box, configure the node pool.

    For more information, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md). The following list describes some of the parameters:

    |Parameter|Description|
    |---------|-----------|
    |**Quantity**|Specify the initial number of nodes in the node pool. If you do not want to create nodes in the node pool, set this parameter to 0.|
    |**Operating System**|Select an operating system for the nodes. The CentOS, Aliyun Cloud Linux, and Windows operating systems are supported.|
    |**ECS Label**|You can add labels to the selected ECS instances.|
    |**Node Label**|You can add labels to the nodes in the node pool.|
    |**Public IP**|If you select **Assign a Public IPv4 Address to Each Node**, public IPv4 addresses are assigned to the nodes in the node pool. You can connect to a node through the assigned IPv4 address. For more information about public IP addresses, see [Public IP addresses](/intl.en-US/Network/Instance IP addresses/IP addresses of ECS instances within VPCs.md). **Note:** If you select **Assign a Public IPv4 Address to Each Node**, do not select **Bind EIP** when you configure auto scaling. Otherwise, nodes cannot be added to the node pool. |
    |**Custom Resource Group**|You can specify the resource group to which the nodes in the node pool belong.|

7.  Click **Confirm Order**.

    On the Node Pools page, if the **state** of the node pool is **Initializing**, it indicates that the node pool is being created. After the node pool is created, the **state** of the node pool changes to **Active**.

    ![nodepool](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5365359951/p95881.png)


## What to do next

After the node pool is created, you can go to the Node Pools page and perform the following operations on the node pool:

-   Click **Details** in the **Actions** column to view the details of the node pool.
-   Click **Edit** in the **Actions** column to modify the configurations of the node pool. For more information, see [Create a node pool](#section_eq0_lmv_4a7).

    **Note:** When you modify the configurations of the node pool, you can update the image version of the node operating system.

-   Click **Scale Out** in the **Actions** column to add nodes to the node pool. For more information, see [Scale out a node pool](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Scale out a node pool.md).

**Related topics**  


[Overview](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Overview.md)

[Scale out a node pool](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Scale out a node pool.md)

