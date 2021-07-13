---
keyword: [preemptible instance, existing instance, set the ratio of preemptible instances to existing instances]
---

# Set the ratio of preemptible instances to existing instances in a node pool

You can set the ratio of preemptible instances to existing instances in a node pool. This allows you to reduce costs by controlling the number of preemptible instances when the node pool contains sufficient existing instances. This topic describes how to set the ratio of preemptible instances to existing instances in a node pool for a Container Service for Kubernetes \(ACK\) cluster.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   The ACK cluster must run Kubernetes 1.9 or later.

**Note:**

-   By default, a cluster can contain at most 100 nodes. To increase the quota, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).
-   When you add an existing Elastic Compute Service \(ECS\) instance to a node pool, make sure that the ECS instance is associated with an elastic IP address \(EIP\) or a NAT gateway is configured for the virtual private cloud \(VPC\) where the ECS instance is deployed. In addition, make sure that the ECS instance can access the Internet. Otherwise, you cannot add the ECS instance.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.

5.  Select a node pool or create a node pool.

    -   Find the node pool that you want to modify and click **Edit** in the **Actions** column.
    -   On the Node Pools page, click **Create Node Pool** in the upper-right corner. For more information, see [Manage a node pool](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Manage a node pool.md).
6.  In the **Create Node Pool** or **Edit Node Pool** dialog box, select multiple vSwitches for **VSwitch**, and select **Preemptible Instance** as **Billing Method**.

7.  Click **Show Advanced Options**. Select **Cost Optimization** for **Multi-Zone Scaling Policy** and set the **Percentage of Pay-as-you-go Instances** and **Enable Supplemental Pay-as-you-go Instances** parameters to meet your business requirements.

    The following table describes the parameters.

    |Parameter|Description|Default|
    |---------|-----------|-------|
    |Multi-zone Scaling Policy|    -   Priority: Nodes are selected based on the order of instance types that you select.
    -   Cost Optimization: Nodes are selected based on the cost of instance types. The instance type with a lower cost is prioritized.
    -   Distribution Balancing: Nodes are selected based on the node distribution across zones. This policy aims to evenly distribute nodes across multiple zones.
|Distribution Balancing|
    |Percentage of Pay-as-you-go Instances|The percentage of pay-as-you-go instances that are created in the node pool. This number does not include the guaranteed minimum number of ECS instances in the node pool.|20|
    |Enable Supplemental Pay-as-you-go Instances|Specifies whether to automatically create pay-as-you-go instances to reach the expected number when preemptible instances are insufficient. For example, preemptible instances are out of stock or your bidding price is higher than the market price.|Enable|

    **Note:**

    After you click **Confirm Order**:

    -   The value of **Multi-zone Scaling Policy** cannot be changed.
    -   If you select **Cost Optimization** for **Multi-zone Scaling Policy**, you can change the values of **Percentage of Pay-as-you-go Instances** and **Enable Supplemental Pay-as-you-go Instances**.
8.  Click **Confirm Order**.


After you complete the configuration, you can find the node pool on the Node Pools page. Click **Details** in the **Actions** column to view **Percentage of Pay-as-you-go Instances** in the **Node Configurations** section.

