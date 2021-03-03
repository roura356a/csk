---
keyword: [Kubernetes 1.16, dynamic expansion of disk type PVs]
---

# Use FlexVolume to dynamically expand a disk type PV in ACK

In Kubernetes 1.16, the feature to dynamically expand a disk type persistent volume \(PV\) is in public preview. Container Service for Kubernetes \(ACK\) allows you to dynamically expand a disk type PV by using FlexVolume in Kubernetes 1.16 and later. This topic describes how to dynamically expand a disk type PV by using FlexVolume.

The **RAM role** of the cluster that you want to manage is granted the ResizeDisk permission. Grant the ResizeDisk permission to the RAM role of the cluster based on the type and plug-in of the cluster.

-   A dedicated cluster that has the CSI plug-in installed:
    1.  Log on to the [ACK console](https://cs.console.aliyun.com).
    2.  In the left-side navigation pane, click **Clusters**.
    3.  On the Clusters page, find the cluster that you want to manage and click **Details** in the **Actions** column.
    4.  In the left-side navigation pane, click **Cluster Information**.
    5.  Click the **Cluster Resources** tab and click the hyperlink next to **Master RAM Role**.
    6.  In the RAM console, grant the ResizeDisk permission to the RAM role. For more information, see [Modify a custom policy](/intl.en-US/Policy Management/Custom policies/Modify a custom policy.md).

        ![resizedisk](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9845359951/p101021.jpg)

-   A managed cluster or a dedicated cluster that has the FlexVolume plug-in installed:

    Repeat the first four steps of the preceding procedure. On the management page of the cluster, click the Cluster Resources tab and click the hyperlink next to **Worker RAM Role**.********


You can choose to manually expand **PVs** or automatically expand **file systems**. ACK allows you to expand PVs and file systems without the need to unmount PVs from the host directory. However, to ensure the stability of the file system, we recommend that you stop the application service and unmound the PV from the host directory first.

To meet different levels of stability requirements, Alibaba Cloud provides the following solutions for PV expansion:

-   Expand a PV without the need to restart the pod to which the PV is mounted. If the I/O throughput of the cluster is high when you use this method, a file system error may occur.
-   Expand a PV during the restart of the pod to which the PV is mounted. To ensure data security, stop application services before you expand PVs.

For Kubernetes 1.16 and later, ACK allows you to expand PVs without the need to restart pods.

## Usage notes

-   **Limits**

    You can dynamically expand only disk PVs that are smaller than 20,000 GiB.

-   **Data backup**

    To avoid data loss caused by PV expansion errors, make sure that snapshots are created for the PV.

-   **Scenarios**
    -   You can dynamically expand only PVs that have the StorageClassName parameter specified.
    -   ACK does not allow you to expand inline volumes. Inline volumes refer to volumes other than PVs and PVCs.
    -   ACK does not allow you to dynamically expand basic disks.
    -   The **allowVolumeExpansion** field of the StorageClass is set to true. You must manually set a StorageClass to true if it is manually declared. StorageClasses that are declared by ACK are automatically set to true.
-   **Plug-in version**

    Make sure that the FlexVolume or CSI plug-in is upgraded to the latest version.


## Expand a disk type PV without restarting the pod

1.  [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

    Assume that the pod that you want to manage is in the following state.

    Run the following command to query information about the pod:

    ```
    kubectl get pod
    ```

    The following output is returned:

    ```
    web-0         1/1     Running   0          42s
    ```

    Run the following command to view the mounting state of the pod:

    ```
    kubectl exec web-0 df /data
    ```

    The following output is returned:

    ```
    Filesystem     1K-blocks  Used Available Use% Mounted on
    /dev/vdb        20511312 45080  20449848   1% /data
    ```

    Run the following command to query information about the persistent volume claim \(PVC\):

    ```
    kubectl get pvc
    ```

    The following output is returned:

    ```
    NAME             STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS              AGE
    disk-ssd-web-0   Bound    d-wz9hpoifm43yn9zie6gl   20Gi       RWO            alicloud-disk-available   57s
    ```

    Run the following command to query information about the PV that is related to the PVC:

    ```
    kubectl get pv
    ```

    The following output is returned:

    ```
    NAME                     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS     CLAIM                    STORAGECLASS              REASON   AGE
    d-wz9hpoifm43yn9zie6gl   20Gi       RWO            Delete           Bound      default/disk-ssd-web-0   alicloud-disk-available            65s
    ```

2.  Make sure that the requirements described in the [Usage notes](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Disk volumes/Use CSI to dynamically expand a disk PV in ACK.md) section are met. Then, run the following command to expand the PV:

    ```
    kubectl patch pvc disk-ssd-web-0 -p '{"spec":{"resources":{"requests":{"storage":"30Gi"}}}}'
    ```

    Wait one minute and perform the following steps to check whether the PV is expanded.

    Run the following command to query information about the PV:

    ```
    kubectl get pv d-wz9hpoifm43yn9zie6gl
    ```

    The following output is returned:

    ```
    NAME                     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                    STORAGECLASS              REASON   AGE
    d-wz9hpoifm43yn9zie6gl   30Gi       RWO            Delete           Bound    default/disk-ssd-web-0   alicloud-disk-available            5m23s
    ```

    Run the following command to query information about the PVC:

    ```
    kubectl get pvc
    ```

    The following output is returned:

    ```
    NAME             STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS              AGE
    disk-ssd-web-0   Bound    d-wz9hpoifm43yn9zie6gl   30Gi       RWO            alicloud-disk-available   5m10s
    ```

    Run the following command to view the mounting state of the pod:

    ```
    kubectl exec web-0 df /data
    ```

    The following output is returned:

    ```
    Filesystem     1K-blocks  Used Available Use% Mounted on
    /dev/vdb        30832548 45036  30771128   1% /data
    ```

    To expand a disk volume without restarting a pod, you only need to run the preceding command to complete all the required operations.


## Expand a disk type PV after restarting a pod

1.  Use kubectl to connect to a Kubernetes cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

    In this example, the pod that you want to manage is in the following state.

    Run the following command to query the information about the pod:

    ```
    kubectl get pod
    ```

    The following output is returned:

    ```
    web-0         1/1     Running   0          42s
    ```

    Run the following command to view the mounting state of the pod:

    ```
    kubectl exec web-0 df /data
    ```

    The following output is returned:

    ```
    Filesystem     1K-blocks  Used Available Use% Mounted on
    /dev/vdb        20511312 45080  20449848   1% /data
    ```

    Run the following command to query the information about the persistent volume claim \(PVC\):

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

2.  Run the following command to view the scheduling information about the PV:

    ```
    kubectl get pv d-wz9g2j5qbo37r2lamkg4  -oyaml | grep failure-domain.beta.kubernetes.io/zone
        failure-domain.beta.kubernetes.io/zone: cn-shenzhen-e
    ```

3.  Modify the label of the scheduled resource by replacing the value of the zone field. Then, the pod to which the PV is mounted cannot be scheduled.

    For example, you can change the value of the zone field in the label from cn-shenzhen-e to cn-shenzhen-e-nozone.

    ```
    kubectl label pv d-wz9g2j5qbo37r2lamkg4 failure-domain.beta.kubernetes.io/zone=cn-shenzhen-e-nozone --overwrite
    persistentvolume/d-wz9g2j5qbo37r2lamkg4 labeled
    ```

4.  Restart the pod.

    The pod temporarily remains in the Pending state because the zone field of the label is modified.

    Run the following command to delete the pod:

    ```
    kubectl delete pod web-0
    ```

    Run the following command to query information about the pod:

    ```
    kubectl get pod
    ```

    The following output is returned:

    ```
    web-0   0/1     Pending   0          27s
    ```

5.  Run the following command to expand the PV:

    ```
    kubectl patch pvc disk-ssd-web-0 -p '{"spec":{"resources":{"requests":{"storage":"30Gi"}}}}'
    ```

6.  Change the zone field in the label to the previous setting to restart the pod. In this example, change the value from cn-shenzhen-e-nozone to cn-shenzhen-e.

    ```
    kubectl label pv d-wz9g2j5qbo37r2lamkg4 failure-domain.beta.kubernetes.io/zone=cn-shenzhen-e --overwrite
    persistentvolume/d-wz9g2j5qbo37r2lamkg4 labeled
    ```

    Wait one minute and perform the following steps to check whether the PV is expanded.

    Run the following command to query information about the pod:

    ```
    kubectl get pod
    ```

    The following output is returned:

    ```
    web-0   1/1     Running   0          3m23s
    ```

    Run the following command to query information about the PVC:

    ```
    kubectl get pvc
    ```

    The following output is returned:

    ```
    disk-ssd-web-0   Bound    d-wz9g2j5qbo37r2lamkg4   30Gi       RWO            alicloud-disk-available   17m
    ```

    Run the following command to query information about the PV:

    ```
    kubectl get pv d-wz9g2j5qbo37r2lamkg4
    ```

    The following output is returned:

    ```
    d-wz9g2j5qbo37r2lamkg4   30Gi       RWO            Delete           Bound    default/disk-ssd-web-0   alicloud-disk-available            17m
    ```

    Run the following command to view the mounting status of the pod:

    ```
    kubectl exec web-0 df /data
    ```

    The following output is returned:

    ```
    /dev/vdb        30832548 45036  30771128   1% /data
    ```

    The result shows that the PV is expanded from 20 GiB to 30 GiB.


