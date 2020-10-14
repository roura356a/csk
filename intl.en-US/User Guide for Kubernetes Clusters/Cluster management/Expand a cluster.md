# Expand a cluster

This topic describes how to scale out the worker nodes in a cluster in the Container Service for Kubernetes console.

Master nodes cannot be scaled out in a cluster.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to expand and select **Expand** from the **More** drop-down list in the **Actions** column.

4.  On the Expand page, set the scale-out parameters.

    The following table describes the required parameters.

    |Parameter|Description|
    |---------|-----------|
    |Region|The region where the cluster is deployed.|
    |Container Runtime|The default runtime is **Docker 19.03.5**.|
    |VPC|Set the network for the nodes. You can select a Virtual Private Cloud \(VPC\) network from the drop-down list. If no VPC network is available, click **Create VPC**. For more information, see [Create a VPC](/intl.en-US/VPCs and VSwitches/VPC management/Create a VPC.md).|
    |VSwitch|Select one or more VSwitches for the cluster.You can select up to three VSwitches deployed in different **zones**. If no VSwitch is available, you can click **Create VSwitch** to create one. For more information, see [t2436.md\#](/intl.en-US/VPCs and VSwitches/VSwitch management/Create a VSwitch.md). |
    |Billing Method|The **pay-as-you-go** and **subscription** billing methods are supported. For more information, see [Comparison of billing methods](/intl.en-US/Pricing/Billing methods/Comparison of billing methods.md). If you select the **subscription** billing method,    -   set the **Duration** parameter. Duration: You can select 1, 2, 3, or 6 months. If you require a longer duration, you can select 1 to 5 years.
    -   Specify whether to enable **Auto Renewal**. |
    |Instance Type|You can select one or more instance types. For more information, see [Instance families](/intl.en-US/Instance/Instance families.md).|
    |Selected Types|The specified instance types appear here.|
    |System Disk|**ESSDs**, **SSDs**, and **ultra disks** are supported.

**Note:** You can select **Enable Backup** to back up disk data. |
    |Mount Data Disk|**ESSDs**, **SSDs**, and **ultra disks** are supported. You can enable disk **encryption** and **backup** when you add data disks. |
    |Nodes to Add|The number of nodes to be added to the cluster. You can add up to 100 nodes to a cluster for the current account. You can add up to 500 nodes at a time.|
    |Logon Type|    -   Key Pair:
        -   **Key Pair**: Select an SSH key pair from the drop-down list.
        -   **create a key pair**: Create a key pair if no SSH key pair is available. For more information about how to create an SSH key pair, see [Create an SSH key pair](/intl.en-US/Security/Key pairs/Use an SSH key pair/Create an SSH key pair.md). After the key pair is created, set it as the credential for logging on to the cluster.
    -   Password:
        -   **Password**: Enter a password for logging on to the nodes.
        -   **Confirm Password**: Enter the password again. |
    |ECS Label|You can attach labels to the selected ECS instances.|
    |Node Label|You can attach labels to the nodes of the cluster.|
    |Taints|Add taints to all worker nodes in the cluster.|
    |Custom Image|You can select a custom image, and then use the image to deploy all nodes in the cluster. For more information about how to create a custom image, see [Create a Kubernetes cluster by using a custom image](/intl.en-US/Best Practices/Cluster/Create a Kubernetes cluster by using a custom image.md).

**Note:** This feature is available to only users in the whitelist. If you are not in the whitelist, [submit a ticket](https://selfservice.console.aliyun.com/ticket/scene/ecs/%E4%BA%91%E6%9C%8D%E5%8A%A1%E5%99%A8%20ECS/detail). |
    |RDS Whitelist|Set the Relational Database Service \(RDS\) whitelist. Add the IP addresses of cluster nodes to the RDS whitelist.**Note:** To enable an RDS instance to access a Kubernetes cluster, you must deploy the RDS instance in the same VPC network as the Kubernetes cluster. |
    |User Data|For more information, see [Prepare user data](/intl.en-US/Instance/Manage instances/User data/Prepare user data.md).|

5.  Click **OK**.

    On the Node Pools page, **Scaling** appears in the **Status** column of the node pool. This indicates that the scale-out event is in progress. After the scale-out event is completed, the **Status** column of the node pool changes to **Active**.


Click **Details** in the Actions column of the node pool. On the **Nodes** tab, you can check the nodes that are added to the node pool of the cluster.

