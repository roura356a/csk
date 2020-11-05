# Use a disk in the ACK console

Pods cannot share a disk. You can mount a disk to one pod only. This topic describes how to use a disk in the Container Service for Kubernetes \(ACK\) console.

-   A Kubernetes cluster is created and the CSI plug-in is deployed to the cluster.
-   A pay-as-you-go disk is created. For more information, see [Create a disk](/intl.en-US/Block Storage/Cloud disks/Create a cloud disk/Create a pay-as-you-go disk.md).

## Create a PV

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Persistent Volumes**.

5.  On the **Persistent Volumes** tab, click **Create** in the upper-right corner.

6.  In the **Create PV** dialog box, configure the parameters.

    -   **PV Type**: You can select Cloud Disk, NAS, or OSS. In this example, select Cloud Disk.
    -   **Volume Plug-in**: You can select Flexvolume or CSI. In this example, select CSI.
    -   **Access Mode**: By default, the value is ReadWriteOnce.
    -   **Disk ID**: Select a disk that is located in the same region and zone as those of the cluster. The disk must be in the Pending state.
    -   **File System Type**: Specify how data is stored on a disk. Valid values: ext4, ext3, xfs, and vfat. Default value: ext4.
    -   **Label**: Attach labels to the persistent volume \(PV\).
7.  Click **Create**.


## Create a PVC

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Persistent Volumes**.

5.  On the **Persistent Volume Claims** tab, click **Create** in the upper-right corner.

6.  In the **Create PVC** dialog box, configure the parameters.

    -   **PVC Type**: You can select Cloud Disk, NAS, or OSS. In this example, select Cloud Disk.
    -   **Name**: the name of the persistent volume claim \(PVC\). The name must be unique in the namespace.
    -   **Allocation Mode**: Select Existing Volumes.

        **Note:** If no PV is created, you can set **Allocation Mode** to **Create Volume**, and configure volume parameters. For more information, see [Create a PV](#section_kuh_z2k_6r2).

    -   **Existing Volumes**: Click **Select PV**. Find the PV that you want to use and click **Select** in the Actions column.
    -   **Capacity**: the capacity of the PVC.

        **Note:** The capacity of the PVC cannot exceed the capacity of the PV.

    -   **Access Mode**: By default, the value is ReadWriteOnce.
7.  Click **Create**.

    After you create the PVC, you can find it on the Persistent Volume Claims tab. In this example, a PVC named csi-disk-pvc is created and a PV is bound to the PVC.


## Create an application

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Workload**.

5.  On the **Deployments** tab, click **Create from Image**.

6.  On the **Basic Information** wizard page, configure basic settings.

    ![Basic settings](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7534749951/p10973.png)

    |Parameter|Description|
    |---------|-----------|
    |Name|The name of the application.|
    |Namespace|The namespace in which the application is deployed. If you do not set this field, the system uses the default namespace.|
    |Replicas|The number of pods in an application. Default value: 2.|
    |Type|The type of the application. You can select **Deployments**, **StatefulSets**, **Jobs**, **Cron Jobs**, and **DaemonSets**.|
    |Label|Add one or more labels to the application.|
    |Annotations|Add one or more annotations to the application.|
    |Synchronize Timezone|Specify whether to synchronize the timezone from nodes to containers.|

    **Note:** In this example, **Deployments** is selected as the application type.

7.  Click **Next**.

    The **Container** step appears.

8.  Configure the container parameters.

    This example describes how to configure the volume parameters. For more information about other parameters, see [Use an image to create a stateless application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an image to create a stateless application.md).

    You can add local volumes and cloud volumes.

    -   **Local storage**: You can select HostPath, ConfigMap, Secret, or EmptyDir from the PV Type drop-down list. Then, specify Mount Source and Container Path to mount a source path to a container path. For more information, see [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE).
    -   **Cloud storage**: You can add cloud volumes.
    In this example, specify csi-disk-pvc as Mount Source and mount it to the /tmp path in the container. In this case, data generated in the /tmp path is stored on the disk.

    ![volume](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6301854061/p179736.png)

9.  Set other parameters and click **Create**.

    After you create the application, you can use the disk in the application.


You can also use a disk by running commands. For more information, see [Use static disk volumes](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Disk volumes/Use static disk volumes.md).

