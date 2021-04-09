---
keyword: [Kubernetes 1.16, dynamically expand disk PVs]
---

# Use CSI to dynamically expand a disk volume in ACK

In Kubernetes 1.16, the feature that allows you to dynamically expand a disk volume is in public preview. Container Service for Kubernetes \(ACK\) allows you to dynamically expand a disk volume by using Container Storage Interface \(CSI\) in Kubernetes 1.16 and later. This topic describes how to dynamically expand a disk volume by using CSI.

You can expand a PV that contains a **disk** or **file system** without the need to unmount the PV from the directory. However, we recommend that you stop the application and unmount the PV from the directory for best practice if the PV contains a file system.

To ensure stability, ACK allows you to use the following methods to expand a PV:

-   Expand a volume without the need to restart the pod to which the volume is mounted. If the I/O throughput of the cluster is high when you use this method, a file system error may occur.
-   Restart the pod to which the PV is mounted and then expand the PV. This method ensures higher security because the PV is expanded after the application is stopped.

Kubernetes 1.16 and later allow you to expand a PV without the need to restart the pod to which the PV is mounted.

## Usage notes

-   **Limits**

    You can dynamically expand only disk volumes that are smaller than 2,000 GiB.

-   **Data backup**

    To avoid data loss caused by volume expansion errors, make sure that you have created a snapshot for the PV.

-   **Scenarios**
    -   You can dynamically expand only volumes that are dynamically provisioned. Dynamically provisioned volumes refer to persistent volumes \(PVs\) that contain the StorageClassName attribute.
    -   ACK does not allow you to expand inline disk volumes. Inline disk volumes refer to disk volumes that are not created by using PVs and persistent volume claims \(PVCs\).
    -   ACK does not allow you to dynamically expand volumes that contain basic disks.
    -   Specify **AllowVolumeExpansion: True** for the StorageClass. The AllowVolumeExpansion field is automatically set to true for StorageClasses that are declared by ACK. For StorageClasses that are manually declared, you must manually set the AllowVolumeExpansion field to true.
-   **Plug-in version**

    Make sure that the FlexVolume or CSI plug-in is upgraded to the latest version.


## Grant the ResizeDisk permission to the RAM role

Before you dynamically expand a disk volume, you must grant the ResizeDisk permission to the **RAM role** of the cluster. To grant the ResizeDisk permission to the RAM role of a cluster based on the type and plug-in of the cluster, perform the following steps:

**A dedicated Kubernetes cluster that has the CSI plug-in installed**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click **Details** in the **Actions** column.

4.  In the left-side navigation pane, click **Cluster Information**.

5.  Click the **Cluster Resources** tab and click the hyperlink next to **Master RAM Role**.

6.  In the RAM console, grant the ResizeDisk permission to the RAM role. For more information, see [Modify a custom policy](/intl.en-US/Policy Management/Custom policies/Modify a custom policy.md).

    ![resizedisk](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9845359951/p101021.jpg)


**A managed Kubernetes cluster or a dedicated Kubernetes cluster that has the FlexVolume plug-in installed**

Repeat the preceding four steps. On the management page of the cluster, click the Cluster Resources tab and then click the hyperlink next to **Worker RAM Role**.

## Expand a disk volume without the need to restart the pod

1.  Use kubectl to connect to a Kubernetes cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).

    In this example, the pod that you want to manage is in the following state.

    Run the following command to query the information about the pod:

    ```
    kubectl get pod
    ```

    The following output is returned:

    ```
    web-0         1/1     Running   0          42s
    ```

    Run the following command to view the mounting details of the pod:

    ```
    kubectl exec web-0 df /data
    ```

    The following output is returned:

    ```
    Filesystem     1K-blocks  Used Available Use% Mounted on
    /dev/vdb        20511312 45080  20449848   1% /data
    ```

    Run the following command to query the information about the PVC:

    ```
    kubectl get pvc
    ```

    The following output is returned:

    ```
    NAME             STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS              AGE
    disk-ssd-web-0   Bound    d-wz9hpoifm43yn9zie6gl   20Gi       RWO            alicloud-disk-available   57s
    ```

    Run the following command to query the information about the PV:

    ```
    kubectl get pv
    ```

    The following output is returned:

    ```
    NAME                     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS     CLAIM                    STORAGECLASS              REASON   AGE
    d-wz9hpoifm43yn9zie6gl   20Gi       RWO            Delete           Bound      default/disk-ssd-web-0   alicloud-disk-available            65s
    ```

2.  After you ensure that the requirements described in the [Usage notes](#section_p0l_kfz_nhq) section are met, run the following command to expand the volume:

    ```
    kubectl patch pvc disk-ssd-web-0 -p '{"spec":{"resources":{"requests":{"storage":"30Gi"}}}}'
    ```

    Wait 1 minute and then check whether the volume is expanded.

    Run the following command to query the information about the PV:

    ```
    kubectl get pv d-wz9hpoifm43yn9zie6gl
    ```

    The following output is returned:

    ```
    NAME                     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                    STORAGECLASS              REASON   AGE
    d-wz9hpoifm43yn9zie6gl   30Gi       RWO            Delete           Bound    default/disk-ssd-web-0   alicloud-disk-available            5m23s
    ```

    Run the following command to query the information about the PVC:

    ```
    kubectl get pvc
    ```

    The following output is returned:

    ```
    NAME             STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS              AGE
    disk-ssd-web-0   Bound    d-wz9hpoifm43yn9zie6gl   30Gi       RWO            alicloud-disk-available   5m10s
    ```

    Run the following command to view the mounting details of the pod:

    ```
    kubectl exec web-0 df /data
    ```

    The following output is returned:

    ```
    Filesystem     1K-blocks  Used Available Use% Mounted on
    /dev/vdb        30832548 45036  30771128   1% /data
    ```

    To expand a disk volume without the need to restart the pod, you need to run only the preceding command.


## Restart the pod to which a disk volume is mounted and then expand the disk volume

1.  Use kubectl to connect to a Kubernetes cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).

    In this example, the pod that you want to manage is in the following state.

    Run the following command to query the information about the pod:

    ```
    kubectl get pod
    ```

    The following output is returned:

    ```
    web-0         1/1     Running   0          42s
    ```

    Run the following command to query the mounting details of the pod:

    ```
    kubectl exec web-0 df /data
    ```

    The following output is returned:

    ```
    /dev/vdb        20511312 45080  20449848   1% /data
    ```

    Run the following command to query the information about the PVC:

    ```
    kubectl get pvc
    ```

    The following output is returned:

    ```
    disk-ssd-web-0   Bound    d-wz9g2j5qbo37r2lamkg4   20Gi       RWO            alicloud-disk-available   7m4s
    ```

    Run the following command to query the information about the PV:

    ```
    kubectl get pv d-wz9g2j5qbo37r2lamkg4
    ```

    The following output is returned:

    ```
    d-wz9g2j5qbo37r2lamkg4   20Gi       RWO            Delete           Bound    default/disk-ssd-web-0   alicloud-disk-available            7m18s
    ```

2.  Run the following command to add a label to the PV for pod scheduling. Set the value of the label to a string that does not correspond to a zone.

    ```
    kubectl label pv d-wz9g2j5qbo37r2lamkg4 failure-domain.beta.kubernetes.io/zone=cn-nozone
    persistentvolume/d-wz9g2j5qbo37r2lamkg4 labeled
    ```

3.  Restart the pod.

    The pod scheduling setting is modified. Therefore, the pod temporarily remains in the Pending state.

    Run the following command to delete the pod:

    ```
    kubectl delete pod web-0
    ```

    Run the following command to query the information about the pod:

    ```
    kubectl get pod
    ```

    The following output is returned:

    ```
    web-0   0/1     Pending   0          27s
    ```

4.  Run the following command to expand the volume:

    ```
    kubectl patch pvc disk-ssd-web-0 -p '{"spec":{"resources":{"requests":{"storage":"30Gi"}}}}'
    ```

5.  Restart the pod by deleting the pod scheduling label of the PV.

    ```
    kubectl label pv d-wz9g2j5qbo37r2lamkg4 failure-domain.beta.kubernetes.io/zone-
    ```

    The following output is returned:

    ```
    persistentvolume/d-wz9g2j5qbo37r2lamkg4 labeled
    ```

    Wait 1 minute and then check whether the volume is expanded.

    Run the following command to query the information about the pod:

    ```
    kubectl get pod
    ```

    The following output is returned:

    ```
    web-0   1/1     Running   0          3m23s
    ```

    Run the following command to query the information about the PVC:

    ```
    kubectl get pvc
    ```

    The following output is returned:

    ```
    disk-ssd-web-0   Bound    d-wz9g2j5qbo37r2lamkg4   30Gi       RWO            alicloud-disk-available   17m
    ```

    Run the following command to query the information about the PV:

    ```
    kubectl get pv d-wz9g2j5qbo37r2lamkg4
    ```

    The following output is returned:

    ```
    d-wz9g2j5qbo37r2lamkg4   30Gi       RWO            Delete           Bound    default/disk-ssd-web-0   alicloud-disk-available            17m
    ```

    Run the following command to query the mounting details of the pod:

    ```
    kubectl exec web-0 df /data
    ```

    The following output is returned:

    ```
    /dev/vdb        30832548 45036  30771128   1% /data
    ```

    The result indicates that the volume is expanded from 20 GiB to 30 GiB.


