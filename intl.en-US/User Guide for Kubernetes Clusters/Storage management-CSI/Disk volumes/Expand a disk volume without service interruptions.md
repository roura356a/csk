---
keyword: [Kubernetes 1.16, dynamically expand a mounted disk]
---

# Expand a disk volume without service interruptions

In Kubernetes 1.16, the feature of expanding a disk volume without service interruptions is in public preview. Container Service for Kubernetes \(ACK\) allows you to use the Container Storage Interface \(CSI\) plug-in to expand a disk volume without service interruptions for clusters of Kubernetes 1.16 and later. This topic describes how to expand a disk volume without service interruptions.

The expansion includes the expansion of the **disk** size and the expansion of the **file system**. Both expansions can be performed without the need to stop the application to which the disk volume is mounted \(the disk and file system are still mounted during the expansion\). However, to ensure the stability of the file system and application, we recommend that you stop the application, unmount the disk volume from the directory, and then expand the disk volume.

**Terms**

-   Expansion without service interruptions

    Expand the mounted disk and file system without stopping the application pods.

-   Expansion with service interruptions

    Expand the mounted disk volume and file system after stopping the application pods, and restart the pods after the expansion is completed.


Kubernetes 1.16 and later allow you to expand a volume without stopping the pod to which the volume is mounted.

## Usage notes

-   **Limits**

    You can expand only disks that are smaller than 2,000 GiB without service interruptions.

-   **Data backup**

    To avoid data loss caused by errors during the expansion, we recommend that you first create a snapshot for the disk to back up the disk data.

-   **Scenarios**
    -   You can expand only dynamically provisioned persistent volumes \(PVs\) without service interruptions, which are mounted by using persistent volume claims \(PVCs\) that contain the StorageClassName parameter.
    -   You cannot expand disk volumes of the inline type. This type of disk volume is not created by using PVs and PVCs.
    -   You cannot expand volumes that are associated with basic disks without service interruptions.
    -   **AllowVolumeExpansion: True** must be specified for the StorageClass. The AllowVolumeExpansion parameter is automatically set to True for StorageClasses that are created by ACK. For StorageClasses that are manually created, you must manually set the AllowVolumeExpansion parameter to True.
-   **Plug-in version**

    Make sure that the FlexVolume or CSI plug-in is upgraded to the latest version.


## Grant the ResizeDisk permission to the Resource Access Management \(RAM\) role of the cluster

Before you expand a disk volume without service interruptions, you must grant the ResizeDisk permission to the **RAM role** of the cluster. Perform the following steps based on the cluster type and the volume plug-in that is used:

**Dedicated Kubernetes clusters that use CSI**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click **Details** in the **Actions** column.

4.  In the left-side navigation pane, click **Cluster Information**.

5.  Click the **Cluster Resources** tab and click the hyperlink next to **Master RAM Role**.

6.  In the RAM console, grant the ResizeDisk permission to the RAM role. For more information, see [Modify a custom policy](/intl.en-US/Policy Management/Custom policies/Modify a custom policy.md).

    ![resizedisk](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9845359951/p101021.jpg)


**Managed or dedicated Kubernetes clusters that use FlexVolume**

Perform the preceding Step 1 to Step 4 and click the hyperlink next to **Master RAM Role**.

## Expand a mounted disk volume without stopping the application

1.  Connect to a Kubernetes cluster by using kubectl. For more information, see [Connect to an ACK cluster by using kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md).

    In this example, the pod that you want to manage is in the following state.

    Run the following command to query information about the pod:

    ```
    kubectl get pod
    ```

    Expected output:

    ```
    web-0         1/1     Running   0          42s
    ```

    Run the following command to query the volume that is mounted to the pod:

    ```
    kubectl exec web-0 df /data
    ```

    Expected output:

    ```
    Filesystem     1K-blocks  Used Available Use% Mounted on
    /dev/vdb        20511312 45080  20449848   1% /data
    ```

    Run the following command to query information about the PVC:

    ```
    kubectl get pvc
    ```

    Expected output:

    ```
    NAME             STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS              AGE
    disk-ssd-web-0   Bound    d-wz9hpoifm43yn9zi****   20Gi       RWO            alicloud-disk-available   57s
    ```

    Run the following command to query information about the PV:

    ```
    kubectl get pv
    ```

    Expected output:

    ```
    NAME                     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS     CLAIM                    STORAGECLASS              REASON   AGE
    d-wz9hpoifm43yn9zi****   20Gi       RWO            Delete           Bound      default/disk-ssd-web-0   alicloud-disk-available            65s
    ```

2.  Make sure that the requirements described in the [Usage notes](#section_p0l_kfz_nhq) section are met, and then run the following command to expand the PV:

    ```
    kubectl patch pvc disk-ssd-web-0 -p '{"spec":{"resources":{"requests":{"storage":"30Gi"}}}}'
    ```

    Wait 1 minute and then check whether the PV is expanded.

    Run the following command to query information about the PV:

    ```
    kubectl get pv d-wz9hpoifm43yn9zi****
    ```

    Expected output:

    ```
    NAME                     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                    STORAGECLASS              REASON   AGE
    d-wz9hpoifm43yn9zi****   30Gi       RWO            Delete           Bound    default/disk-ssd-web-0   alicloud-disk-available            5m23s
    ```

    Run the following command to query information about the PVC:

    ```
    kubectl get pvc
    ```

    Expected output:

    ```
    NAME             STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS              AGE
    disk-ssd-web-0   Bound    d-wz9hpoifm43yn9zi****   30Gi       RWO            alicloud-disk-available   5m10s
    ```

    Run the following command to query the volume that is mounted to the pod:

    ```
    kubectl exec web-0 df /data
    ```

    Expected output:

    ```
    Filesystem     1K-blocks  Used Available Use% Mounted on
    /dev/vdb        30832548 45036  30771128   1% /data
    ```

    To expand a disk volume without service interruptions, you only need to run the preceding command.


