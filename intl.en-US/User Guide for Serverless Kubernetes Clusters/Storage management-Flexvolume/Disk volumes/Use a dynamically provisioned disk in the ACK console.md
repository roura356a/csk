# Use a dynamically provisioned disk in the ACK console

This topic describes how to use disks to create dynamically provisioned persistent volumes \(PVs\) in the Container Service for Kubernetes \(ACK\) console.

[Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md)

## Create a StorageClass

1.  Log on to the [ACK console](https://cs.console.aliyun.com)[ACK console](https://partners-intl.console.aliyun.com/#/cs).

2.  In the left-side navigation pane, click **Serverless Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volumes**.

5.  On the **Persistent Volumes** page, click the **Storage Classes** tab and click **Create**in the upper-right corner of the page. In the **Create** dialog box, set the required parameters.

    -   **Name**: the name of the disk.
    -   **PV Type**: Set the value to Cloud Disk. This specifies that the provisioner plug-in for Alibaba Cloud disks is used to create the StorageClass.
    -   **Volume Plug-in**: In this example, **Flexvolume** is selected.
    -   **Parameters**: In this example, the sample parameters are type and zoneid.
        -   type: the disk type. Valid values include cloud\_efficiency, cloud\_ssd, cloud\_essd, and available. If you set this parameter to available, the system attempts to create a disk until it succeeds. The system selects a disk type in sequence from this list: an enhanced SSD \(ESSD\), a standard SSD, and an ultra disk. The system keeps trying until a disk is created.
        -   zoneid: This parameter specifies the region where a disk is created.

            For a multi-zone cluster, you can specify multiple zones. Example:

            ```
            zoneid: cn-hangzhou-a,cn-hangzhou-b,cn-hangzhou-c
            ```

        -   encrypted: optional. This parameter specifies whether a created disk is encrypted. By default, this parameter is set to false. This specifies that a created disk is not encrypted.
    -   **Reclaim Policy**: the policy that is used to reclaim a disk. By default, this parameter is set to Delete. You can also set this parameter to Retain. If you require high data security, we recommend that you set this parameter to Retain to avoid data loss caused by user errors.
    -   **Binding Mode**: Valid values: Immediate and WaitForFirstConsumer. Default value: Immediate.
    -   **Mount Options**: When you mount a volume, you can add multiple mount options.
6.  After you complete the settings, click **Create**.


## Create a PVC

1.  Log on to the [ACK console](https://cs.console.aliyun.com)[ACK console](https://partners-intl.console.aliyun.com/#/cs).

2.  In the left-side navigation pane, click **Serverless Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volumes**.

5.  On the **Persistent Volume Claims** tab, click **Create** in the upper-right corner of the page. In the Create PVC dialog box, set the required parameters.

    -   **PVC Type**: **Cloud Disk**, **NAS**, and **OSS** are supported. In this example, **Cloud Disk** is selected.
    -   **Name**: the name of the PV. The name must be unique in the cluster.
    -   **Allocation Mode**: **Use StorageClass**, **Existing Volumes**, and **Create Volume** are supported. In this example, **Use StorageClass** is selected.
    -   **Existing Storage Class**: Click **Select**. Find the StorageClass that you want to use and click **Select** in the Actions column of the StorageClass.
    -   **Capacity**: the capacity of the PVC.

        **Note:** The capacity of the PV cannot exceed the disk capacity.

    -   **Access Mode**: Default value: ReadWriteOnce.
6.  After you complete the settings, click **Create**.

    After the PVC is created, test-cloud appears in the list of PVCs and the PVC is associated with the specified PV.


## Use the dynamically provisioned disk

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
    |Type|The type of application. You can select **Deployments**, **StatefulSets**, **Jobs**, ******Cron Jobs**, or **DaemonSets**.|
    |Label|Add a label to the application. The label is used to identify the application.|
    |Annotations|Add an annotation to the application.|
    |Synchronize Timezone|Specify whether to synchronize the time zone between nodes and containers.|

    **Note:** **Deployments** is selected in this example.

7.  Configure containers.

    This example provides further details about the parameters in the Volume section. For more information about other parameters, see [Deploy a stateless application from an image](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Deploy a stateless application from an image.md).

    Local storage and cloud storage are supported.

    -   **Local Storage**: You can select HostPath, ConfigMap, Secret, or EmptyDir. The source directory or file is mounted to a path in the container. For more information, see [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE).
    -   **Cloud Storage**: supports the following types of persistent volumes \(PVs\): cloud disks, Network Attached Storage \(NAS\), and Object Storage Service \(OSS\).
    In this example, a PV is created from a cloud disk, and the PV is mounted to the /tmp path in the container. The data that is generated in this path is stored in the cloud disk.

8.  After all information is configured, click **Create**.

    After the data volume is created, you can use the data volume.


You can also run commands to create dynamically provisioned PVs. For more information, see [Dynamically provision an Alibaba Cloud disk as a PV by using the CLI](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Disk volumes/Dynamically provision an Alibaba Cloud disk as a PV by using the CLI.md).

