# Expand an ACK cluster

This topic describes how to scale out the worker nodes in a cluster of Container Service for Kubernetes \(ACK\) in the ACK console.

Master nodes cannot be scaled out in an ACK cluster.

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to expand and choose **More** \> **Expand** in the **Actions** column.

4.  On the Expand page, set the scale-out parameters.

    |Parameter|Description|
    |---------|-----------|
    |Region|This parameter is automatically set to the region where the cluster is deployed.|
    |Container Runtime|This parameter is automatically set to **Docker 19.03.5**.|
    |VPC|Set the network for the nodes. You can select a virtual private cloud \(VPC\) from the drop-down list. If no VPC is available, click **Create VPC** to create one. For more information, see [Create a VPC](/intl.en-US/VPCs and VSwitches/VPC management/Create a VPC.md).|
    |VSwitch|Set the vSwitches.You can select up to three vSwitches that are deployed in different **zones**. If no vSwitch is available, click **Create VSwitch** to create one. For more information, see [t2436.md\#](/intl.en-US/VPCs and VSwitches/VSwitch management/Create a VSwitch.md). |
    |Billing Method|The **pay-as-you-go** and **subscription** billing methods are supported. For more information, see [Billing method overview](/intl.en-US/Pricing/Billing methods/Billing method overview.md). If you select the **subscription** billing method,    -   the **Duration** parameter is required. You can select 1, 2, 3, or 6 months. If you require a longer duration, you can select 1 to 5 years.
    -   Specify whether to enable **Auto Renewal**. |
    |Instance Type|You can select one or more instance types. For more information, see [Instance families](/intl.en-US/Instance/Instance families.md).|
    |Selected Types|The selected instance types are displayed.|
    |System Disk|**Enhanced SSDs**, **SSDs**, and **ultra disks** are supported.

**Note:** You can select **Enable Backup** to back up disk data. |
    |Mount Data Disk|**Enhanced SSDs**, **SSDs**, and **ultra disks** are supported. You can enable disk **encryption** and **backup** when you mount data disks. |
    |Nodes to Add|The number of nodes to be added to the cluster. You can add up to 100 nodes to a cluster under the current account. You can add up to 500 nodes at a time.|
    |Logon Type|    -   Set the key pair.
        -   **Key Pair**: Select an SSH key pair from the drop-down list.
        -   **create a key pair**: Create an SSH key pair if no SSH key pair is available. For more information about how to create an SSH key pair, see [Create an SSH key pair](/intl.en-US/Security/Key pairs/Use an SSH key pair/Create an SSH key pair.md). After the key pair is created, set it as the credential that is used to log on to the ACK cluster.
    -   Password:
        -   **Password**: Enter the password that is used to log on to the nodes.
        -   **Confirm Password**: Enter the password again. |
    |ECS Label|You can add labels to the ECS instances.|
    |Node Label|You can add labels to the nodes of the cluster.|
    |Taints|You can add taints to all worker nodes in the cluster.|
    |Custom Image|You can select a custom image for the nodes to be added. After you select a custom image, the nodes to be added are deployed based on the image. For more information about how to create a custom image, see [Create a Kubernetes cluster by using a custom image](/intl.en-US/Best Practices/Cluster/Create a Kubernetes cluster by using a custom image.md).

**Note:**

    -   Only custom images based on CentOS 7.x and Alibaba Cloud Linux 2.x are supported. Before you use a custom image to deploy the nodes, you must add the nodes to the cluster in **manual** mode. For more information about how to add nodes to a cluster in Manual mode, see [Add ECS instances in manual mode](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Add existing ECS instances to an ACK cluster.md).
    -   This feature is available to only users in the whitelist. If you are not in the whitelist, [submit a ticket](https://selfservice.console.aliyun.com/ticket/scene/ecs/%E4%BA%91%E6%9C%8D%E5%8A%A1%E5%99%A8%20ECS/detail). |
    |RDS whitelist|Set the Relational Database Service \(RDS\) whitelist. Add the IP addresses of the nodes in the ACK cluster to the RDS whitelist.**Note:** To enable an RDS instance to access an ACK cluster, you must deploy the RDS instance in the same VPC as the ACK cluster. |
    |User Data|For more information, see [Prepare user data](/intl.en-US/Instance/Manage instances/User data/Prepare user data.md).|

5.  Click **Confirm**.

    On the Node Pools page, if the **Status** column shows **Scaling** for the node pool, it indicates that the node pool is being scaled out. After the node pool is scaled out, the **Status** column shows **Active** for the node pool.


Click **Details** in the Actions column. On the **Nodes** tab, you can view information about the nodes that are added to the node pool.

