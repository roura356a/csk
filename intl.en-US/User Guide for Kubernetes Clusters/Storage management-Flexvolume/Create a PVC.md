# Create a PVC

In the Container Service for Kubernetes \(ACK\) console, you can create a persistent volume claim \(PVC\).

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   A persistent volume \(PV\) is created. In this example, a PV is created based on a cloud disk. For more information, see [Use Alibaba Cloud disks as volumes](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Disk volumes/Use Alibaba Cloud disks as volumes.md).

    By default, PVCs are associated with PVs that have the alicloud-pvname label. This label is added to the PVs that are created in the ACK console. If a PV does not have this label, add the label to the PV before you can associate the PV with a PVC.


1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click **Details** in the **Actions** column.

4.  In the left-side navigation pane, click **Persistent Volumes**.

5.  On the **Persistent Volumes Claims** tab, click **Create**.

6.  In the **Create PVC** dialog box, set the parameters and click **Create**.

    -   **PVC Type**: Cloud Disk, NAS, and OSS are supported. These options are also available when you set PVC Type for a PV.
    -   **Name**: the name of the PVC.
    -   **Allocation Mode**: **Use StorageClass**, **Existing Volumes**, and **Create Volume** are supported. In this example, **Use StorageClass** or **Existing Volumes** is selected.
    -   **Existing Storage Class**: Click **Select PV**. In the Select PV dialog box, find the PV that you want to use and click **Select** in the Actions column of the PV.

        **Note:** This parameter is required only when you set Allocation Mode to **Use StorageClass**.

    -   **Existing Volumes**: Click **Select PV**. In the Select PV dialog box, find the PV that you want to use and click **Select** in the Actions column of the PV.

        **Note:** This parameter is required only when you set Allocation Mode to **Existing Volumes**.

    -   **Capacity**: the claimed usage. The value cannot be larger than the total capacity of the associated PV.
    -   **Access Mode**: Default value: ReadWriteOnce.

        **Note:** This parameter is required only when you set Allocation Mode to **Use StorageClass**.

    **Note:** Your cluster may have an unused PV that does not appear in the **Select PV** dialog box. The possible reason is that the alicloud-pvname label has not been added to the PV.

    If you cannot find available PVs, go to the Cluster Information page. In the left-side navigation pane, click **Persistent Volumes**. The Persistent Volume Claims tab appears. Click the Persistent Volumes tab, find the PV that you want to use, and then click **Manage Labels** in the Actions column of the PV. You can add a label to the PV and set the label name to alicloud-pvname and the value to the PV name. If the PV is created from a disk, the disk ID is used as the PV name.

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6745359951/p10717.png)

7.  On the **Persistent Volume Claims** tab, the newly created PVC appears.


