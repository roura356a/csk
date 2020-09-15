# Expand a Kubernetes cluster that supports sandboxed containers

You can adjust the number of worker nodes in a cluster based on business needs through the console.

Currently, you cannot adjust the number of master nodes in a cluster.

To expand a Kubernetes cluster that supports sandboxed containers, you must set the following parameters as required in the following table.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  Select the target cluster and click **Expand** in the Actions column.

4.  Go to the Expand page and set the parameters.

    In this example, the number of worker nodes in the cluster is increased from three to five. The following table describes the required parameters.

    |Parameter|Description|
    |---------|-----------|
    |Cluster Name|The name of the cluster.|
    |Region|The region where the cluster is deployed.|
    |VPC|Set the Virtual Private Cloud \(VPC\) network of the cluster. You can select a VPC network from the drop-down list. If no VPC network is available, you can click **Create VPC** to create one. For more information, see [Create a VPC](/intl.en-US/VPCs and VSwitches/VPC management/Create a VPC.md).|
    |VSwitch|Set the VSwitch. Select one to three VSwitches. We recommend that you select VSwitches in different **zones**. If no VSwitch is available, click **Create VSwitch** to create one. For more information, see [t2436.md\#](/intl.en-US/VPCs and VSwitches/VSwitch management/Create a VSwitch.md). |
    |Billing Method|Kubernetes clusters support **pay-as-you-go** and **subscription** nodes. If you select **subscription**,     -   you need to set the **Duration**. Valid values: 1, 2, 3, 6, and 12. Unit: months.
    -   you need to set whether to enable **Auto Renewal**. |
    |Existing Worker Nodes|The number of existing worker nodes in the cluster.|
    |Nodes to Add|The number of worker nodes that you want to add to the cluster.|
    |Worker Nodes After Scaling|The expected number of worker nodes in the cluster after the scale-out.|
    |Instance Type|Select **ECS Bare Metal Instance**.|
    |Selected Types|Selected instance types appear here.|
    |System Disk|Solid-state drive \(SSD\) and ultra disks are supported.|
    |Mount Data Disk|SSD, ultra disks, and basic disks are supported. **Note:** You can enable disk encryption and backup when you select disks. Data disks are used to store the root file systems of containers on the nodes. You must mount a data disk of 200 GiB at least. We recommend that you mount a data disk of 1 TB or above. |
    |Logon Type|    -   Key Pair:

If no key pair is available, you can click **create a key pair** to create one in the ECS console. For more information, see [Create an SSH key pair](/intl.en-US/Security/Key pairs/Use an SSH key pair/Create an SSH key pair.md). After the key pair is created, set it as the credentials to log on to the cluster.

    -   Password:
        -   **Password**: Set the logon password.
        -   **Confirm Password**: Enter the logon password again. |
    |Key Pair|
    |CloudMonitor Agent|Select whether to install the CloudMonitor agent. After the CloudMonitor agent is installed on an ECS node, you can view monitoring information about the node through the CloudMonitor console.|
    |RDS Whitelist|Set the Relational Database Service \(RDS\) whitelist. Add the IP addresses of cluster nodes to the RDS whitelist.|
    |Labels|Attach labels to new nodes. Enter a key and value, and click **Add**. **Note:**

    -   The key is required whereas the value is optional.
    -   The key is case insensitive and can be up to 64 characters in length. It cannot start with any of the following strings: aliyun, http://, or https://.
    -   The value is optional, case insensitive, and can be up to 128 characters in length. It cannot start with string http:// or https://.
    -   The key must be unique among the labels attached to the same resource. If you specify a duplicate key when you create a label, the existing label will be overwritten.
    -   You can attach up to 20 labels to a resource. To attach more labels, you must remove existing labels. |
    |Custom Image|You can select a custom image. All nodes in the cluster will be deployed based on the image. For more information about custom imags, see [Create a cluster by using a custom image](/intl.en-US/Best Practices/Cluster/Create a cluster by using a custom image.md). **Note:** This feature is available to selected users only. To use this feature, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
    |Taints|Add taints to the nodes.|
    |CPU Policy|Set the CPU policy.     -   none: This is the default policy, which indicates that the default CPU affinity is used.
    -   static: This policy allows pods with certain resource characteristics to be granted increased CPU affinity and exclusivity on the node. |
    |User Data|For more information, see [Prepare user data](/intl.en-US/Instance/Manage instances/User data/Prepare user data.md).|

5.  Click **Submit**.


In the left-side navigation pane, choose **Clusters** \> **Nodes** to go to the Nodes page. The number of worker nodes is now changed to five.

