# Use a statically provisioned disk in the ACK console

Pods cannot share a disk. Each disk can be mounted to only one pod. This topic describes how to enable multiple pods to share a disk by creating and mounting a static volume in the Container Service for Kubernetes \(ACK\) console.

-   An ACK cluster is created and the CSI plug-in is deployed in the cluster.
-   A pay-as-you-go disk is created. For more information, see [Create a disk](/intl.en-US/Block Storage/Cloud disks/Create a cloud disk/Create a disk.md).

## Create a PV

1.  Log on to the [ACK console](https://cs.console.aliyun.com)[ACK console](https://partners-intl.console.aliyun.com/#/cs).

2.  In the left-side navigation pane, click **Serverless Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volumes**.

5.  On the **Persistent Volumes** page, click **Create** in the upper-right corner.

6.  In the **Create PV** dialog box, set the following parameters:

    -   **PV Type**: Select Cloud Disk, NAS, or OSS based on your business requirements. In this example, Cloud Disk is selected.
    -   **Volume Plug-in**: Select Flexvolume or CSI based on your business requirements. In this example, CSI is selected.
    -   **Access Mode**: The default is ReadWriteOnce.
    -   **Disk ID**: Select a disk that is created in the same region and zone as the cluster. The disk must be in the Pending state.
    -   **File System Type**: Select the file system that the disk uses to store data. You can select ext4, ext3, xfs, or vfat. The default is ext4.
    -   **Label**: The labels that you want to add to the PV.
7.  Click **Create**.


## Create a PVC

1.  Log on to the [ACK console](https://cs.console.aliyun.com)[ACK console](https://partners-intl.console.aliyun.com/#/cs).

2.  In the left-side navigation pane, click **Serverless Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volumes**.

5.  On the **Persistent Volume Claims** page, click **Create** in the upper-right corner.

6.  In the **Create PVC** dialog box, set the following parameters:

    -   **PVC Type**: Select Cloud Disk, NAS, or OSS based on your business requirements. In this example, Cloud Disk is selected.
    -   **Name**: Enter a name for the PVC. The name must be unique in the namespace.
    -   **Allocation Mode**: In this example, Existing Volumes is selected.

        **Note:** If no PV is created, you can set **Allocation Mode** to **Create Volume**, and set the parameters to create a PV. For more information, see [Create a PV](#section_qop_mue_s8h).

    -   **Existing Volumes**: Click **Select PV**, find the PV that you want to use, and then click **Select** in the Actions column.
    -   **Capacity**: Set the capacity of the PV.

        **Note:** The capacity of the PV cannot exceed the capacity of the disk.

    -   **Access Mode**: The default is ReadWriteOnce.
7.  Click **Create**.

    After you create the PVC, you can find it on the Persistent Volume Claims page. In this example, a PVC named csi-disk-pvc is created and a PV is associated with the PVC.


## Deploy an application

1.  Log on to the [ACK console](https://cs.console.aliyun.com)[ACK console](https://partners-intl.console.aliyun.com/#/cs).

2.  In the left-side navigation pane, click **Serverless Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  On the **Deployments** tab, click **Create from Image**.

6.  On the **Basic Information** wizard page, configure the basic settings.

    ![Basic configuration](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8309301161/p10973.png)

    |Parameter|Description|
    |---------|-----------|
    |Name|The name of the application.|
    |Namespace|The namespace where you want to deploy the application. The default namespace is automatically selected. You can select another namespace.|
    |Replicas|The number of pods that are provisioned for the application. Default value: 2.|
    |Type|The type of application. You can select **Deployments**, **StatefulSets**, **Jobs**, **Cron Jobs**, or **DaemonSets**.|
    |Label|Add a label to the application. The label is used to identify the application.|
    |Annotations|Add an annotation to the application.|
    |Synchronize Timezone|Specify whether to synchronize the time zone between nodes and containers.|

    **Note:** **Deployments** is selected in this example.

7.  Click **Next**.

    The **Container** wizard page appears.

8.  Set the container parameters.

    This example shows how to set the volume parameters. For more information about other parameters, see [Deploy a stateless application from an image](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Deploy a stateless application from an image.md).

    You can add local volumes and cloud volumes.

    -   **Add Local Storage**: You can select HostPath, ConfigMap, Secret, or EmptyDir from the PV Type drop-down list. Then, set the Mount Source and Container Path parameters to mount the volume to a container path. For more information, see [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE).
    -   **Add PVC**: You can add cloud volumes.
    Mount the disk volume that is created in this example to the /tmp path of the container. After the disk volume is mounted, container data that is generated in the /tmp path is saved to the disk volume.

    ![](../images/p68586.png)

9.  Set other parameters and click **Create**.

    After you create the application, you can use the disk volume to store application data.


You can also use the statically provisioned disk through the command-line interface \(CLI\). For more information, see [Mount a cloud disk as a static volume by using a YAML template](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Disk volumes/Mount a cloud disk as a static volume by using a YAML template.md).

