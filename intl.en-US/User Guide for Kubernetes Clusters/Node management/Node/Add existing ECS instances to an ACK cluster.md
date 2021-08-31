---
keyword: [node management, add nodes]
---

# Add existing ECS instances to an ACK cluster

You can add existing Elastic Compute Service \(ECS\) instances to a Container Service for Kubernetes \(ACK\) cluster in the ACK console. ECS instances can be added to an ACK cluster only as worker nodes. This topic describes how to manually and automatically add ECS instances to an ACK cluster.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   The ECS instances that you want to add belong to the security group of the worker nodes in the ACK cluster. The security group is automatically created when you initialize the cluster.

**Note:**

Before you use the cluster, take note of the following limits:

-   By default, you can deploy at most 100 nodes in each cluster. To add more nodes, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).
-   The ECS instances that you want to add to the ACK cluster must be deployed in the same region and virtual private cloud \(VPC\) as the cluster.
-   The ECS instances that you want to add must belong to the same account as the cluster.
-   Nodes that run the following operating systems can be added to an ACK cluster:
    -   Alibaba Cloud Linux 2
    -   CentOS 7.x. CentOS 8.x and later are not supported.
    -   Windows Server 2019 and Windows Server version 1909.

## Automatically add ECS instances

In auto mode, all ECS instances that are available within your account are listed. You can select, configure, and add one or more ECS instances to a cluster in the ACK console. After you complete the configurations, the ECS instances are automatically added to the cluster.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Nodes**.

5.  On the Nodes page, click **Add Existing Node** in the upper-right corner.

6.  On the **Select Existing ECS Instance** wizard page, select the auto mode to automatically add ECS instances to the cluster.

    Set **Mode** to **Auto** and select the ECS instances that you want to add in the Select Existing ECS Instance section.

7.  Click **Next Step** and set the parameters on the **Specify Instance Information** wizard page.

    |Parameter|Description|
    |---------|-----------|
    |**Cluster ID/Name**|Information about the cluster to which the instances are to be added. This parameter is automatically set.|
    |**Data Disk**|Specify whether to store the container and image data on a data disk.     -   If the ECS instances have data disks mounted and the file system of the last data disk is not initialized, the system automatically formats the data disk to ext4. Then, the system mounts the data disk to /var/lib/docker and /var/lib/kubelet.

**Note:** After the data disk is formatted, the data that is stored on the disk is erased. Make sure that you have backed up the data before you add the ECS instances to the ACK cluster.

    -   If no data disk is attached to the ECS instances, the system does not purchase a new data disk. |
    |**Retain Instance Name**|By default, **Retain Instance Name** is turned on. If you do not want to retain the instance name, you can turn off **Retain Instance Name**. After you disable this feature, the node is renamed based on the node naming rules.|
    |**Instance Information**|The IDs and names of the instances to be added.|

8.  Click **Next Step**. In the Confirm message, click **Confirm**.


## Manually add ECS instances

**Note:** ECS instances that are manually added to an ACK cluster are not released when the ACK cluster is deleted.

In manual mode, you must obtain the installation command, log on to an ECS instance, and then run the command to add the ECS instance to an ACK cluster. You can add only one ECS instance at a time.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Nodes**.

5.  On the Nodes page, click **Add Existing Node** in the upper-right corner.

6.  On the **Select Existing ECS Instance** wizard page, select the manual mode to add ECS instances to the cluster.

    Set **Mode** to **Manual** and select the ECS instances that you want to add in the Select Existing ECS Instance section.

7.  Click **Next Step** and configure the parameters on the **Specify Instance Information** wizard page.

    |Parameter|Description|
    |---------|-----------|
    |**Cluster ID/Name**|Information about the cluster to which the instances are to be added. This parameter is automatically set.|
    |**Data Disk**|Specify whether to store the container and image data on a data disk.     -   If the ECS instances have data disks mounted and the file system of the last data disk is not initialized, the system automatically formats the data disk to ext4. Then, the system mounts the data disk to /var/lib/docker and /var/lib/kubelet.

**Note:** After the data disk is formatted, the data that is stored on the disk is erased. Make sure that you have backed up the data before you add the ECS instances to the ACK cluster.

    -   If no data disk is attached to the ECS instances, the system does not purchase a new data disk. |
    |**Retain Instance Name**|By default, **Retain Instance Name** is turned on. If you do not want to retain the instance name, you can turn off **Retain Instance Name**. After you disable this feature, the node is renamed based on the node naming rules.|
    |**Instance Information**|The IDs and names of the instances to be added.|

8.  Click **Next Step** to go to the Complete wizard page. On the Complete wizard page, copy the command and click **Done**.

9.  Log on to the [ECS console](https://ecs.console.aliyun.com). In the left-side navigation pane, choose **Instances & Images** \> **Instances**. Select the region where the cluster is deployed and then select the ECS instances that you want to add.

10. Click **Connect** in the Actions column. In the Connection and Command dialog box, select a connection method and go to the connection page.

    The following table describes the connection methods.

    |Connection method|Description|
    |-----------------|-----------|
    |**VNC Connection**|For more information about how to connect to an ECS instance by using Virtual Network Computing \(VNC\), see [Connect to a Linux instance by using password authentication](/intl.en-US/Instance/Connect to instances/Connect to an instance by using VNC/Connect to a Linux instance by using password authentication.md) or [Connect to a Windows instance by using password authentication](/intl.en-US/Instance/Connect to instances/Connect to an instance by using VNC/Connect to a Windows instance by using password authentication.md).|
    |**Send Remote Commands \(Cloud Assistant\)**|This is the recommended method. This method allows you to run remote commands on an instance. You can perform operations such as viewing disk capacity, installing software, and starting or stopping services without logging on to the instance. This feature is implemented by using Cloud Assistant. For more information about how to install and activate the Cloud Assistant client, see [Install the Cloud Assistant client](/intl.en-US/Deployment & Maintenance/Cloud assistant/Configure the Cloud Assistant client/Install the Cloud Assistant client.md).|

11. On the connection page, follow the instructions and paste the command copied in [Step 8](#step_7cz_ibv_9eb). Then, click **Run** to execute the script.

    After the script is executed, the ECS instance is added to the cluster.


In the left-side navigation pane of the cluster details page, choose **Nodes** \> **Nodes**. On the Nodes page, you can view the newly added node.

