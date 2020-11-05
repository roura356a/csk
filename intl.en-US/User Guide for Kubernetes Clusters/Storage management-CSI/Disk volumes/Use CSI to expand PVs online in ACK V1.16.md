---
keyword: [ACK V1.16, online PV expansion]
---

# Use CSI to expand PVs online in ACK V1.16

Container Service for Kubernetes \(ACK\) V1.16 allows you to expand persistent volumes \(PVs\) online. This feature is in public preview. This topic describes how to enable online PV expansion by using the Container Storage Interface \(CSI\) plug-in.

A **RAM role** of the cluster that you want to manage is granted the ResizeDisk permission. The steps for granting the ResizeDisk permission to a RAM role vary based on the cluster type and the plug-in type.

-   A dedicated cluster that has the CSI plug-in:
    1.  Log on to the [ACK console](https://cs.console.aliyun.com).
    2.  In the left-side navigation pane, click **Clusters**.
    3.  On the Clusters page, find the cluster that you want to manage and click **Details** in the **Actions** column.
    4.  In the left-side navigation pane, click **Cluster Information**.
    5.  Click the **Cluster Resources** tab and click the link next to **Master RAM Role**.
    6.  In the RAM console, grant the ResizeDisk permission to the RAM role. For more information, see [Modify a custom policy](/intl.en-US/Policy Management/Custom policies/Modify a custom policy.md).

        ![resizedisk](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/9845359951/p101021.jpg)

-   A managed cluster or a dedicated cluster that has the FlexVolume plug-in:

    Perform the first four steps of the preceding procedure. On the management page of the cluster, click the Cluster Resources tab and click the link next to **Worker RAM Role**. Then, grant the ResizeDisk permission to the RAM role.


Online PV expansion consists of the manual expansion of **PVs** and the automatic expansion of **file systems**. ACK allows you to expand both PVs and file systems without unmounting PVs from the host directory. To ensure the stability of file system expansion, we recommend that you stop application services and unmount the PV from the host directory first.

To meet different levels of stability requirements, ACK provides the following solutions for PV expansion:

-   Expand a PV without restarting the pod to which the PV is mounted. If you use this method, file system errors may occur if the cluster I/O is high.
-   Expand a PV during the restart of the pod to which the PV is mounted. To ensure data security during expansion, we recommend that you stop application services before expansion.

By default, ACK V1.16 and later support PV expansion without restarting the pod.

## Usage notes

-   **Data backup**

    To avoid data loss caused by PV expansion errors, make sure that snapshots are created for the PV.

-   **Scenarios**
    -   You can enable online expansion for a PV only if the StorageClassName parameter is specified for the PV.
    -   ACK does not support expansion of inline volumes.
    -   ACK does not support online expansion of basic disks.
    -   The **AllowVolumeExpansion** field of a StorageClass is set to true. If you create a StorageClass, you must set this field to true.
-   **Plug-in version**

    Make sure that the FlexVolume or CSI plug-in is upgraded to the latest version.


## Expand a PV online without restarting the pod

1.  Use kubectl to connect to a cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

    Run the following command to query the pod:

    ```
    kubectl get pod
    ```

    The following command output appears:

    ```
    web-0         1/1     Running   0          42s
    ```

    Run the following command to query the file system mounted to the pod:

    ```
    kubectl exec web-0 df /data
    ```

    The following command output appears:

    ```
    Filesystem     1K-blocks  Used Available Use% Mounted on
    /dev/vdb        20511312 45080  20449848   1% /data
    ```

    Run the following command to query the persistent volume claim \(PVC\):

    ```
    kubectl get pvc
    ```

    The following command output appears:

    ```
    NAME             STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS              AGE
    disk-ssd-web-0   Bound    d-wz9hpoifm43yn9zie6gl   20Gi       RWO            alicloud-disk-available   57s
    ```

    Run the following command to query the PV:

    ```
    kubectl get pv
    ```

    The following command output appears:

    ```
    NAME                     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS     CLAIM                    STORAGECLASS              REASON   AGE
    d-wz9hpoifm43yn9zie6gl   20Gi       RWO            Delete           Bound      default/disk-ssd-web-0   alicloud-disk-available            65s
    ```

2.  Make sure that the requirements described in [Usage notes](#section_p0l_kfz_nhq) are met. Then, run the following command to expand the PV:

    ```
    kubectl patch pvc disk-ssd-web-0 -p '{"spec":{"resources":{"requests":{"storage":"30Gi"}}}}'
    ```

    Wait a while and check whether the PV is expanded.

    Run the following command to query the PV:

    ```
    kubectl get pv d-wz9hpoifm43yn9zie6gl
    ```

    The following command output appears:

    ```
    NAME                     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                    STORAGECLASS              REASON   AGE
    d-wz9hpoifm43yn9zie6gl   30Gi       RWO            Delete           Bound    default/disk-ssd-web-0   alicloud-disk-available            5m23s
    ```

    Run the following command to query the PVC:

    ```
    kubectl get pvc
    ```

    The following command output appears:

    ```
    NAME             STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS              AGE
    disk-ssd-web-0   Bound    d-wz9hpoifm43yn9zie6gl   30Gi       RWO            alicloud-disk-available   5m10s
    ```

    Run the following command to query the file system mounted to the pod:

    ```
    kubectl exec web-0 df /data
    ```

    The following command output appears:

    ```
    Filesystem     1K-blocks  Used Available Use% Mounted on
    /dev/vdb        30832548 45036  30771128   1% /data
    ```

    In this scenario, only one command is required for PV expansion.


## Expand a PV online during the restart of the pod

1.  Use kubectl to connect to a cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

    Run the following command to query the pod:

    ```
    kubectl get pod
    ```

    The following command output appears:

    ```
    web-0         1/1     Running   0          42s
    ```

    Run the following command to query the file system mounted to the pod:

    ```
    kubectl exec web-0 df /data
    ```

    The following command output appears:

    ```
    /dev/vdb        20511312 45080  20449848   1% /data
    ```

    Run the following command to query the PVC:

    ```
    kubectl get pvc
    ```

    The following command output appears:

    ```
    disk-ssd-web-0   Bound    d-wz9g2j5qbo37r2lamkg4   20Gi       RWO            alicloud-disk-available   7m4s
    ```

    Run the following command to query the PV:

    ```
    kubectl get pv d-wz9g2j5qbo37r2lamkg4
    ```

    The following command output appears:

    ```
    d-wz9g2j5qbo37r2lamkg4   20Gi       RWO            Delete           Bound    default/disk-ssd-web-0   alicloud-disk-available            7m18s
    ```

2.  Run the following command to attach a label to the PV for pod scheduling. Specify the value of the label to cn-nozone.

    ```
    kubectl label pv d-wz9g2j5qbo37r2lamkg4 failure-domain.beta.kubernetes.io/zone=cn-nozone
    persistentvolume/d-wz9g2j5qbo37r2lamkg4 labeled
    ```

3.  Restart the pod.

    The pod enters the Pending state because the pod scheduling information is modified.

    Run the following command to delete the pod:

    ```
    kubectl delete pod web-0
    ```

    Run the following command to query the pod:

    ```
    kubectl get pod
    ```

    The following command output appears:

    ```
    web-0   0/1     Pending   0          27s
    ```

4.  Run the following command to expand the PV:

    ```
    kubectl patch pvc disk-ssd-web-0 -p '{"spec":{"resources":{"requests":{"storage":"30Gi"}}}}'
    ```

5.  Modify the value of the PV label to the original value. This operation resumes the pod restart.

    ```
    kubectl label pv d-wz9g2j5qbo37r2lamkg4 failure-domain.beta.kubernetes.io/zone-
    ```

    The following command output appears:

    ```
    persistentvolume/d-wz9g2j5qbo37r2lamkg4 labeled
    ```

    Wait a while and check whether the PV is expanded.

    Run the following command to query the pod:

    ```
    kubectl get pod
    ```

    The following command output appears:

    ```
    web-0   1/1     Running   0          3m23s
    ```

    Run the following command to query the PVC:

    ```
    kubectl get pvc
    ```

    The following command output appears:

    ```
    disk-ssd-web-0   Bound    d-wz9g2j5qbo37r2lamkg4   30Gi       RWO            alicloud-disk-available   17m
    ```

    Run the following command to query the PV:

    ```
    kubectl get pv d-wz9g2j5qbo37r2lamkg4
    ```

    The following command output appears:

    ```
    d-wz9g2j5qbo37r2lamkg4   30Gi       RWO            Delete           Bound    default/disk-ssd-web-0   alicloud-disk-available            17m
    ```

    Run the following command to query the file system mounted to the pod:

    ```
    kubectl exec web-0 df /data
    ```

    The following command output appears:

    ```
    /dev/vdb        30832548 45036  30771128   1% /data
    ```

    The command output shows that the PV is expanded from 20 GiB to 30 GiB.


