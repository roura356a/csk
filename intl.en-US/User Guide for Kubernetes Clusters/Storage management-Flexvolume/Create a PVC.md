---
keyword: [persistent volume, PVC, persistent volume claim]
---

# Create a PVC

This topic describes how to create a persistent volume claim \(PVC\) in the Container Service for Kubernetes \(ACK\) console.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   A persistent volume \(PV\) is created. In this example, the PV is created from a disk. For more information, see [Usage notes for disk volumes](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Disk volumes/Usage notes for disk volumes.md).

    By default, a PVC is associated with a PV that has the alicloud-pvname label. This label is added to all PVs that are created in the ACK console. If a PV does not have this label, you must manually add the label to the PV before you can associate the PV with a PVC.


1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volume Claims**.

5.  In the upper-right corner of the **Persistent Volume Claims** page, click **Create**.

6.  In the **Create PVC** dialog box, set the parameters and click **Create**.

    -   **PVC Type**: The PVC and PV must be of the same type. You can select Cloud Disk, NAS, and OSS.
    -   **Name**: Enter a name for the PVC.
    -   **Allocation Mode**: **Use StorageClass**, **Existing Volumes**, and **Create Volume** are supported. In this example, **Use StorageClass** or **Existing Volumes** is selected.
    -   **Existing Storage Class**: Click **Select**. In the Select Storage Class dialog box, find the StorageClass that you want to use and click **Select** in the Actions column.

        **Note:** This parameter is required only when you set Allocation Mode to **Use StorageClass**.

    -   **Existing Volumes**: Click **Select PV**. In the Select PV dialog box, find the PV that you want to use and click **Select** in the Actions column.

        **Note:** This parameter is required only when you set Allocation Mode to **Existing Volumes**.

    -   **Capacity**: the capacity claimed by the PVC. The value cannot be larger than the total capacity of the associated PV.
    -   **Access Mode**: Default value: ReadWriteOnce.

        **Note:** This parameter is required only when you set Allocation Mode to **Use StorageClass**.

    **Note:** If your cluster has a PV that is not used, but you cannot find it in the **Select PV** dialog box, the reason may be that the PV does not have the alicloud-pvname label.

    If no PV is available, choose **Volumess** \> **Persistent Volumes** in the left-side navigation pane, find the PV that you want to use, and then click **Manage Labels** in the Actions column to add a label to the PV. Set the label name to alicloud-pvname and the value to the PV name. By default, the disk ID is used as the PV name if the PV is created from a disk.

    ![tag](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/6745359951/p10717.png)

7.  Return to the **Persistent Volume Claims** page, you can find the newly created PVC.


