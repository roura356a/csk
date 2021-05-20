# Use a dynamically provisioned disk volume in the ACK console

This topic describes how to use a dynamically provisioned disk volume in the Container Service for Kubernetes \(ACK\) console.

An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

## Create a StorageClass

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Volumes** \> **Storage Classes**.

5.  On the **StorageClasses** page, click **Create**. In the **Create** dialog box that appears, set the following parameters:

    -   **Name**: the name of the disk.
    -   **PV Type**: Set the value to Cloud Disk. This specifies that the provisioner plug-in for Alibaba Cloud disks is used to create the StorageClass.
    -   **Volume Plug-in**: In this example, **Flexvolume** is selected.
    -   **Parameter**: In this example, the parameters are type and zoneid.
        -   type: the disk type. Valid values: cloud\_efficiency, cloud\_ssd, cloud\_essd, and available. If you set this parameter to available, the system attempts to create a disk until it succeeds. The system selects a disk type in sequence from this list: an enhanced SSD \(ESSD\), a standard SSD, and an ultra disk. The system keeps trying until a disk is created.
        -   zoneid: This parameter specifies the region where the disk is created.

            For a multi-zone cluster, you can specify multiple zones. Example:

            ```
            zoneid: cn-hangzhou-a,cn-hangzhou-b,cn-hangzhou-c
            ```

        -   encrypted: optional. This parameter specifies whether the disk to be created is encrypted. By default, this parameter is set to false. This specifies that the disk to be created is not encrypted.
    -   **Reclaim Policy**: the policy that is used to reclaim a disk. By default, this parameter is set to Delete. You can also set this parameter to Retain. If you require higher data security, we recommend that you set this parameter to Retain to avoid data loss caused by user errors.
    -   **Binding Mode**: Valid values: Immediate and WaitForFirstConsumer. Default value: Immediate.
    -   **Mount Options**: When you mount a volume, you can add multiple mount options.
6.  Click **Create**.


## Create a PVC

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volume Claims**.

5.  In the upper-right corner of the **Persistent Volume Claims** page, click **Create**. In the Create PVC dialog box, set the required parameters.

    -   **PVC Type**: **Cloud Disk**, **NAS**, and **OSS** are supported. In this example, **Cloud Disk** is selected.
    -   **Name**: the name of the persistent volume claim \(PVC\). The name must be unique in the namespace.
    -   **Allocation Mode**: **Use StorageClass**, **Existing Volumes**, and **Create Volume** are supported. In this example, **Use StorageClass** is selected.
    -   **Existing Storage Class**: Click **Select**. Find the StorageClass that you want to use and click **Select** in the Actions column.
    -   **Capacity**: the capacity of the PVC.

        **Note:** The capacity of the PVC cannot exceed the capacity of the disk.

    -   **Access Mode**: The default value is ReadWriteOnce.
6.  Click **Create**.

    After the PVC is created, the PVC named test-cloud appears in the list of PVCs. The PVC is associated with the specified PV.


## Use the dynamically provisioned disk volume

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  On the **Deployments** page, click **Create from Image**.

6.  Set the parameters that are required to create a Deployment.

    This example shows how to set the volume parameters. For more information about other parameters, see [Use a Deployment to create a stateless application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Use a Deployment to create a stateless application.md).

    You can add local volumes and cloud volumes.

    -   **Local Storage**: You can select HostPath, ConfigMap, Secret, or EmptyDir. The source directory or file is mounted to a path in the container. For more information, see [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE).
    -   **Cloud Storage**: supports the following types of persistent volumes \(PVs\): disks, Apsara File Storage NAS \(NAS\) file systems, and Object Storage Service \(OSS\).
    In this example, a PV is created from a disk, and the PV is mounted to the /tmp path in the container. The data that is generated in this path is stored in the disk.

7.  Set other parameters and click **Create**.

    After the disk volume is created, you can use the disk volume.


You can also run commands to create a dynamically provisioned disk volume. For more information, see [Use the CLI to dynamically provision a disk as a PV](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Disk volumes/Dynamically provision an Alibaba Cloud disk as a PV by using the CLI.md).

