---
keyword: [node management, add nodes]
---

# Add existing ECS instances to an ACK cluster

You can add existing Elastic Compute Service \(ECS\) instances to a cluster of Container Service for Kubernetes \(ACK\) in the ACK console. ECS instances can be added to an ACK cluster only as worker nodes. This topic describes how to manually and automatically add ECS instances to an ACK cluster.

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   The ECS instances that you want to add belong to the security group of the worker nodes in the ACK cluster. The security group is automatically created when you initialize the cluster.

**Note:**

Before you use the cluster, take note of the following limits:

-   By default, you can deploy up to 100 nodes in an ACK cluster. To add more nodes, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).
-   The ECS instances to be added to the ACK cluster must be deployed in the same region and virtual private cloud \(VPC\) as the cluster.
-   Before you add an existing Elastic Compute Service \(ECS\) instance to an ACK cluster, make sure that an elastic IP address \(EIP\) is attached to the ECS instance, or a Network Address Translation \(NAT\) gateway is created in the VPC where the ECS instance is deployed. This ensures that the ECS instance can access the Internet. Otherwise, the ECS instance cannot be added to the cluster.
-   The ECS instances to be added must belong to the same account as the cluster.
-   Nodes that run the following operating systems can be added to an ACK cluster:
    -   Alibaba Cloud Linux 2
    -   CentOS 7.x. CentOS 8.x and later are not supported.
    -   Windows Server 2009 and Windows Server 2009 Core.

## Add ECS instances in auto mode

In auto mode, all ECS instances that are available under your account are listed. You can select, configure, and add one or more ECS instances to a cluster in the ACK console. After you complete the configurations, the ECS instances are automatically added to the cluster.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  Go to the Add Existing ECS Instance page in the following ways:

    -   Click **More** to go to the **Add Existing ECS Instance** page.
        1.  On the Clusters page, find the cluster that you want to manage and click **More** in the **Actions** column.
        2.  Choose **Add Existing Node** from the **More** drop-down list.
    -   Click **Node Pools** to go to the **Add Existing ECS Instance** page.
        1.  On the Clusters page, find the cluster that you want to manage and click **Node Pools** in the **Actions** column.
        2.  On the Node Pools page, click **Add Existing Node** in the **Actions** column.
    -   Click **Applications** to go to the **Add Existing ECS Instance** page.
        1.  On the Clusters page, find the cluster that you want to manage and click **Applications** in the **Actions** column.
        2.  In the left-side navigation pane of the page that appears, click **Nodes**.
        3.  On the **Nodes** page, click **Add Existing Node**.
4.  Configure the settings on the **Select Existing ECS Instance** wizard page.

    Set **Mode** to **Auto** and select the required ECS instances in the Select Existing ECS Instance section.

5.  Click **Next Step** and configure the settings on the **Specify Instance Information** wizard page.

    |Parameter|Description|
    |---------|-----------|
    |**Container Runtime**|Select a type of container runtime for the nodes. You can select Docker or Sandboxed-Container.|
    |**System Images**|Select an operating system for the nodes.|
    |**Custom Image**|The custom image will replace the default system image.|
    |**Data Disk**|Specify whether to store the container and image data on a data disk.    -   If data disks have been attached to the ECS instances and the file system of the last data disk is not initialized, the system automatically formats the data disk to ext4 and mounts the data disk to /var/lib/docker and /var/lib/kubelet.

**Note:** After the data disk is formatted, the data stored on the disk is cleared. Make sure that you have backed up the data before you add the ECS instances to the ACK cluster.

    -   If no data disk is attached to the ECS instances, the system does not purchase a new data disk. |
    |**CPU Policy**|Set the CPU policy.    -   none: This policy indicates that the default CPU affinity is used. This is the default policy.
    -   static: This policy allows pods with specific resource characteristics on the node to be granted with enhanced CPU affinity and exclusivity. |
    |**Logon Type**|    -   Key Pair:

Select a key pair as the credential that is used to log on to the nodes.

    -   Password:

Enter and confirm the password that is used to log on to the nodes. |
    |**RDS Whitelist**|Select an Relational Database Service \(RDS\) instance to add the ECS instances to the whitelist of the RDS instance.|
    |**Labels**|Add labels to the cluster. Enter keys and values, and click **Add**.

**Note:**

    -   Key is required. Value is optional.
    -   Keys are not case-sensitive. A key must be 1 to 64 characters in length and cannot start with aliyun, http://, or https://.
    -   Values are not case-sensitive. A value must be 1 to 128 characters in length and cannot start with http:// or https://.
    -   The keys of labels that are added to the same resource must be unique. If you add a label with a used key, the label overwrites the one that uses the same key.
    -   You can add up to 20 labels to each resource. If you add more than 20 labels to a resource, all labels become invalid. You must remove unused labels for the remaining labels to take effect. |
    |**Retain Instance Name**|By default, **Retain Instance Name** is turned on. If you do not want to retain the instance name, you can turn off **Retain Instance Name**. After you disable this feature, the node is renamed based on the node naming rules.|
    |**User Data**|For more information, see [Prepare user data](/intl.en-US/Instance/Manage instances/User data/Prepare user data.md).|

6.  Click **Next Step**. In the message that appears, click **Confirm**.


## Add ECS instances in manual mode

In manual mode, you must obtain the installation command, log on to an ECS instance, and then run the command to add the ECS instance to an ACK cluster. You can add only one ECS instance at a time.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  Go to the Add Existing ECS Instance page in the following ways:

    -   Click **More** to go to the **Add Existing ECS Instance** page.
        1.  On the Clusters page, find the cluster that you want to manage and click **More** in the **Actions** column.
        2.  Choose **Add Existing Node** from the **More** drop-down list.
    -   Click **Node Pools** to go to the **Add Existing ECS Instance** page.
        1.  On the Clusters page, find the cluster that you want to manage and click **Node Pools** in the **Actions** column.
        2.  On the Node Pools page, click **Add Existing Node** in the **Actions** column.
    -   Click **Applications** to go to the **Add Existing ECS Instance** page.
        1.  On the Clusters page, find the cluster that you want to manage and click **Applications** in the **Actions** column.
        2.  In the left-side navigation pane of the page that appears, click **Nodes**.
        3.  On the **Nodes** page, click **Add Existing Node**.
4.  Configure the settings on the **Select Existing ECS Instance** wizard page.

    Set **Mode** to **Manual** and select the required ECS instances in the Select Existing ECS Instance section.

5.  Click **Next Step** and configure the settings on the **Specify Instance Information** wizard page.

    |Parameter|Description|
    |---------|-----------|
    |**Data Disk**|Specify whether to store the container and image data on a data disk.    -   If data disks have been attached to the ECS instances and the file system of the last data disk is not initialized, the system automatically formats the data disk to ext4 and mounts the data disk to /var/lib/docker and /var/lib/kubelet.

**Note:** After the data disk is formatted, the data that is stored on the disk is cleared. Make sure that you have backed up the data before you add the ECS instances to the ACK cluster.

    -   The system does not purchase a new data disk for you if no data disk is attached to the ECS instances. |
    |**RDS Whitelist**|Select an RDS instance to add the ECS instances to the whitelist of the RDS instance.|
    |**Retain Instance Name**|By default, **Retain Instance Name** is turned on. If you do not want to retain the instance name, you can turn off **Retain Instance Name**. After you disable this feature, the node is renamed based on the node naming rules.|

6.  Click **Next Step** to go to the Complete wizard page. On the Complete wizard page, copy the command and click **Complete**.

7.  Log on to the [ECS console](https://ecs.console.aliyun.com/). In the left-side navigation pane, click **Instances** and select an ECS instance in the region where the ACK cluster is deployed.

8.  Click **Connect** on the right side of the page. In the dialog box that appears, follow the instructions to connect to the ECS instance. Enter the remote connection password and click **OK**. After you are connected to the ECS instance, paste the command that is copied in Step 7 and click **OK** to execute the script.

    After the script is executed, the ECS instance is added to the cluster.


Go to the Clusters page in the ACK console and click the cluster. In the left-side navigation pane, click **Nodes** to go to the Nodes page. You can view information about the newly added node.

