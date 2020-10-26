---
keyword: [node management, add nodes]
---

# Add existing ECS instances to an ACK cluster

You can add existing Elastic Compute Service \(ECS\) instances to a Container Service for Kubernetes \(ACK\) cluster in the ACK console. Only worker nodes can be added to an ACK cluster. This topic describes how to manually and automatically add ECS instances to the ACK cluster.

-   [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md).
-   The ECS instances that you want to add belong to the security group of the worker nodes in the ACK cluster. The security group is automatically created when you initialize the cluster.

**Note:**

Before you use the cluster, note the following limits:

-   By default, you can deploy up to 100 nodes in an ACK cluster. To add more nodes, you must [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).
-   The ECS instances that you want to add to the ACK cluster must be in the same Virtual Private Cloud \(VPC\) network and region as the cluster.
-   Before you add an existing ECS instance that is deployed in a VPC network, make sure that an elastic IP address is assigned to the ECS instance. Otherwise, you make sure that a Network Address Translation \(NAT\) gateway is created in the same VPC network for the ECS instance. This ensures that the ECS instance can access the Internet. Otherwise, the ECS instance cannot be added to the cluster.
-   The ECS instances must belong to the same account as the cluster.
-   The ECS instances must run the CentOS operating system.

## Add ECS instances in auto mode

In auto mode, all ECS instances that are available under your account are listed. You can select, configure, and add one or more ECS instances to a cluster in the ACK console. After you complete the configuration, the ECS instances are automatically added to the cluster.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  Use one of the following methods to go to the Add Existing ECS Instance page:

    -   Click **More** in the **Actions** column:
        1.  On the Clusters page, find the cluster that you want to manage and choose **More** \> **Add Existing Node**
        2.  in the **Actions** column of the cluster.
    -   Click **Node Pools** in the **Actions** column:
        1.  On the Clusters page, find the cluster that you want to manage and click **Node Pools** in the **Actions** column of the cluster.
        2.  On the Node Pools page, click **Add Existing Node** in the **Actions** column of the node pool to which you want to add nodes.
    -   Click **Applications** in the **Actions** column:
        1.  On the Clusters page, find the cluster that you want to manage and click **Applications** in the **Actions** column of the cluster.
        2.  The Workload page appears. In the left-side navigation pane, click **Nodes**.
        3.  On the **Nodes** page, click **Add Existing Node**.
4.  Configure the **Select Existing ECS Instance** wizard page.

    Set **Mode** to **Auto** and select the required ECS instances in the Select Existing ECS Instance section.

5.  Click **Next Step** and set parameters on the **Specify Instance Information** wizard page.

    |Parameter|Description|
    |---------|-----------|
    |**Container Runtime**|Select a type of container runtime for the nodes. You can select Docker or Sandboxed-Container.|
    |**System Images**|Select an operating system for the nodes.|
    |**Custom Image**|If you select a custom image, the default image will be replaced.|
    |**Data Disk**|Specify whether to store the container and image data on a data disk.    -   The system automatically formats the last data disk to ext4 and mounts the data disk to /var/lib/docker and /var/lib/kubelet. This applies if data disks have been attached to the ECS instances and the file system of the last data disk is not initialized.

**Note:** After the data disk is formatted, the data on the disk is cleared. Before you add the ECS instance to the ACK cluster, you must back up your data to avoid data loss.

    -   If no data disk is attached to the ECS instance, the system does not purchase and assign you a new data disk. |
    |**CPU Policy**|Set the CPU policy.    -   none: the default CPU policy. This policy enables the default CPU affinity scheme.
    -   static: This policy allows pods with specific resource characteristics on the node to be granted with enhanced CPU affinity and exclusivity. |
    |**Logon Type**|    -   Key Pair:

Select a key pair as the credential to log on to the nodes.

    -   Password:

Enter and confirm the password to log on to the nodes. |
    |**RDS Whitelist**|Select an RDS instance to add the ECS instance to the whitelist of the RDS instance.|
    |**Labels**|Attach labels to nodes. Enter keys and values, and click **Add**.

**Note:**

    -   The key field is required and the value field is optional.
    -   Keys are not case-sensitive. A key must be 1 to 64 characters in length and cannot start with aliyun, http://, and https://.
    -   Values are not case-sensitive. A value must be 1 to 128 characters in length and cannot start with http:// or https://.
    -   You can attach multiple labels to an ECS instance. The key of each label must be unique. If you attach a label that has the same key as that of an existing label, the new label overwrites the existing one.
    -   You can attach up to 20 labels to each resource. If you attach more than 20 labels to a resource, all labels become invalid. You must detach unused labels for the other labels to take effect. |
    |**Retain Instance Name**|By default, **Retain Instance Name** is turned on. If you do not want to retain the instance name, you can turn off **Retain Instance Name**. After you disable this feature, the node is renamed based on the node naming rules.|
    |**User Data**|For more information, see [Prepare user data](/intl.en-US/Instance/Manage instances/User data/Prepare user data.md).|

6.  Click **Next Step**. In the message that appears, click **Confirm**.


## Add ECS instances in manual mode

In manual mode, you must obtain the installation command, log on to an ECS instance, and then run the command to add the ECS instance to an ACK cluster. You can add only one ECS instance at a time.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  Use one of the following methods to go to the Add Existing ECS Instance page:

    -   Click **More** in the **Actions** column:
        1.  On the Clusters page, find the cluster that you want to manage and choose **More** \> **Add Existing Node**
        2.  in the **Actions** column of the cluster.
    -   Click **Node Pools** in the **Actions** column:
        1.  On the Clusters page, find the cluster that you want to manage and click **Node Pools** in the **Actions** column of the cluster.
        2.  On the Node Pools page, click **Add Existing Node** in the **Actions** column of the node pool to which you want to add nodes.
    -   Click **Applications** in the **Actions** column:
        1.  On the Clusters page, find the cluster that you want to manage and click **Applications** in the **Actions** column of the cluster.
        2.  In the left-side navigation pane, click **Nodes**.
        3.  On the **Nodes** page, click **Add Existing Node**.
4.  Configure the **Select Existing ECS Instance** wizard page.

    Set **Mode** to **Manual** and select the required ECS instances in the Select Existing ECS Instance section.

5.  Click **Next Step** and set parameters on the **Specify Instance Information** wizard page.

    |Parameter|Description|
    |---------|-----------|
    |**Data Disk**|Specify whether to store the container and image data on a data disk.    -   The system automatically formats the last data disk to ext4 and mounts the data disk to /var/lib/docker and /var/lib/kubelet. This applies if data disks have been attached to the ECS instances and the file system of the last data disk is not initialized.

**Note:** After the data disk is formatted, the data on the disk is cleared. Before you add the ECS instance to the ACK cluster, you must back up your data to avoid data loss.

    -   If no data disk is attached to the ECS instance, the system does not purchase and assign you a new data disk. |
    |**RDS Whitelist**|Select an RDS instance to add the ECS instance to the whitelist of the RDS instance.|
    |**Retain Instance Name**|By default, **Retain Instance Name** is turned on. If you do not want to retain the instance name, you can turn off **Retain Instance Name**. After you disable this feature, the node is renamed based on the node naming rules.|

6.  Click **Next Step** to go to the Complete wizard page. On the Complete wizard page, copy the command and click **Complete**.

7.  Log on to the [ECS console](https://ecs.console.aliyun.com/). In the left-side navigation pane, click **Instances** and select an ECS instance in the region where the ACK cluster is deployed.

8.  Click **Connect** on the right side of the page. In the dialog box that appears, click Connect in the VNC Connection section. On the page that appears, enter the VNC password and click **OK**. After you connect to the instance, paste the command that you copied in Step 6 and click **OK** to execute the script.

    After the script is executed, the ECS instance is added to the cluster.


To check the information about the node that you have added to the cluster, log on to the ACK console, go to the Clusters page, and then click the cluster name. The Cluster Information page appears. In the left-side navigation pane, click **Nodes**. On the Nodes page, you can view the node that you have added to the cluster.

