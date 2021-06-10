---
keyword: [disk volume, manual expansion]
---

# Manually expand a disk volume

In a Container Service for Kubernetes \(ACK\) cluster that runs Kubernetes earlier than 1.16, you cannot set the cluster to automatically expand disk volumes. You must manually expand disk volumes. This topic describes how to manually expand a disk volume.

**Terms**

-   Automatic expansion

    You need only to modify the volume size specified in a persistent volume claim \(PVC\). The modification is automatically applied to expand the corresponding disk volume and the file system.

-   Manual expansion

    You must manually expand the disk volume and run the `resize2fs` command to expand the file system.

-   Expansion without service interruption

    You can expand the disk volume and file system without the need to stop the application.

-   Expansion with service interruption

    You must first stop the application, expand the disk volume and file system, and then start the application again.


To expand a disk volume, perform the following operations:

-   Expand the size of the disk: You must perform this operation in the Elastic Compute Service \(ECS\) console.
-   Expand the file system: You must connect to the ECS instance to which the disk is mounted and then perform this operation.
-   Modify the volume size specified in the persistent volume \(PV\) and PVC. This operation is not supported in current versions.

    **Note:**

    Disk volumes cannot be automatically expanded in earlier Kubernetes versions due to the following reasons. We recommend that you upgrade Kubernetes to the latest version and then modify the volume size specified in the PV and PVC to automatically expand the disk volume.

    -   The procedure for modifying the volume size specified in the PV and PVC varies based on the Kubernetes version.
    -   The volume size specified in the PV and PVC does not affect the use of the underlying storage device. This means that if the volume size specified in a PV and a PVC is 20 GiB and the sizes of the disk and file system are 30 GiB, the application can use at most 30 GiB of storage.

To ensure stability, Container Service for Kubernetes \(ACK\) allows you to use the following methods to expand a disk volume:

-   Manually expand a disk volume without service interruption: If the I/O throughput of the disk is high when you expand the file system, an I/O error may occur in the file system. You do not need to restart the application if you choose this method.
-   Manually expand a disk volume with service interruption: After the application is stopped, the disk I/O operations are stopped. This ensures data security when you expand the file system. Your service will be temporarily interrupted if you choose this method.

## Usage notes

-   **Limits**

    You can expand only disk volumes that are smaller than 2,000 GiB.

-   **Data backup**

    Before you expand a disk volume, you must back up the disk data by creating a snapshot of the disk. This prevents data loss when you expand the disk volume.

-   **Scenarios**

    You cannot automatically expand disk volumes in the following scenarios:

    -   The Kubernetes version of the cluster is earlier than 1.16.
    -   The PV is a statically provisioned disk volume.

## Examples

A stateful application named web is used in this example to demonstrate how to expand a disk volume by using the preceding two methods. Perform the following operations to query information about the disk:

-   Run the following command to query the pods that are provisioned for the web application:

    ```
    kubectl get pod | grep web
    ```

    Expected output:

    ```
    NAME                               READY   STATUS    RESTARTS   AGE
    web-0                              1/1     Running   0          11h
    web-1                              1/1     Running   0          11h
    ```

-   Run the following command to query the PVCs that are created for the web application:

    ```
    kubectl get pvc | grep web
    ```

    Expected output:

    ```
    NAME             STATUS   VOLUME        CAPACITY   ACCESS MODES   STORAGECLASS         AGE
    disk-ssd-web-0   Bound    d-0jlhaq***   20Gi       RWO            alicloud-disk-essd   11h
    disk-ssd-web-1   Bound    d-0jl0j5***   20Gi       RWO            alicloud-disk-essd   11h
    ```

-   Run the following command to query the PVs that are created for the web application:

    ```
    kubectl get pv | grep web
    ```

    Expected output:

    ```
    NAME          CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                    STORAGECLASS        REASON    AGE
    d-0jl0j5***   20Gi       RWO            Delete           Bound    default/disk-ssd-web-1   alicloud-disk-essd            11h
    d-0jlhaq***   20Gi       RWO            Delete           Bound    default/disk-ssd-web-0   alicloud-disk-essd            11h
    ```


The output indicates that two disks named `d-0jl0j5***` and `d-0jlhaq***` are used by the web application. Both disks are 20 GiB in size. The disks are mounted to two pods separately.

For more information about how to deploy a stateful application, see [Use a StatefulSet to create a stateful application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Use a StatefulSet to create a stateful application.md).

## Method 1: Expand the disk volume without service interruption

Find the corresponding disk based on the PV information, manually expand the disk, and then connect to the node to which the disk is mounted and expand the file system. The following example demonstrates how to expand both disks to 30 GiB.

**Step 1: Expand the disks**

1.  Log on to the [ECS console](https://ecs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Storage & Snapshots** \> **Disks**.

3.  Find the disks named d-0jl0j5\*\*\* and d-0jlhaq\*\*\* and check the states of the disks. Then, choose **More** \> **Resize Disk** in the **Actions** column for each disk.

4.  On the Resize Disks page, select **Online Resizing**, and enter the size to which you want to expand the disk in the Size after Resize section. In this example, set the size to 30 GiB.

    **Note:** The specified value cannot be smaller than the current disk size.

5.  Confirm the disk expansion fee. Read and select ECS Service Terms, and then click **Confirm**.

    For more information, see [Resize disks online for Linux instances](/intl.en-US/Block Storage/Resize cloud disks/Resize disks online for Linux instances.md).


**Step 2: Expand the file systems**

After the disks are expanded, you must expand the file systems. Otherwise, the storage that the application can use is still 20 GiB.

**Note:** This step is intended for unpartitioned disks that are used in Kubernetes. We recommend that you do not use partitioned disks in Kubernetes.

-   If an unpartitioned disk is mounted as a PV, you cannot manually create partitions for the disk. Otherwise, the file system may be damaged and data loss may occur.
-   If a partitioned disk is mounted as a PV, you must expand the file system after you can expand the partitioned disk. For more information, see [Step 3: View the disk partitions](/intl.en-US/Block Storage/Resize cloud disks/Resize disks online for Linux instances.md) and [Step 4: Resize partitions](/intl.en-US/Block Storage/Resize cloud disks/Resize disks online for Linux instances.mdsection_kmu_sxr_8jk).

1.  View the ECS instances to which the disks are mounted.

    1.  Log on to the [ECS console](https://ecs.console.aliyun.com).

    2.  In the left-side navigation pane, choose **Storage & Snapshots** \> **Disks**.

    3.  Find the disks named d-0jl0j5\*\*\* and d-0jlhaq\*\*\*, and click the name of each disk.

    4.  On the Details page, click **Attached To** in the **Attaching Information** section.

    5.  On the Instance Details tab, view **Network Information** about the ECS instance.

    **Note:** You can also view the ECS instances to which the disks are mounted in the ACK console. For more information, see [View pods](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Manage pods.md).

2.  Connect to the ECS instance to which the disk is mounted and obtain the driver letter of the disk.

    For more information about how to connect to an ECS instance, see [Methods used to connect to ECS instances](/intl.en-US/Instance/Connect to instances/Overview.md).

    You can use the following methods to obtain the driver letter of the disk.

    -   [Obtain the driver letter of the disk](#step_3q5_c6f_yyn).
    -   Run the following command to query the driver letter of the disk named d-0jlhaq\*\*\*:

        ```
        # Query {pv-name}
        mount |grep d-0jlhaq*** 
        ```

        Expected output:

        ```
        /dev/vdc on /var/lib/kubelet/plugins/kubernetes.io/csi/pv/d-0jlhaq***/globalmount type ext4 (rw,relatime)
        /dev/vdc on /var/lib/kubelet/pods/a26d174f-***/volumes/kubernetes.io~csi/d-0jlhaq***/mount type ext4 (rw,relatime)
        ```

        The output indicates that the driver letter of the `d-0jlhaq***` disk is /dev/vdc.

3.  Run the following command to expand the file system:

    ```
    resize2fs /dev/vdc
    ```

    **Note:** /dev/vdc is the driver letter obtained in [Step 2](#step_o33_77e_v49).

    Expected output:

    ```
    resize2fs 1.43.5 (04-Aug-2017)
    Filesystem at /dev/vdc is mounted on /var/lib/kubelet/plugins/kubernetes.io/csi/pv/d-0jlhaq***/globalmount; on-line resizing required
    old_desc_blocks = 3, new_desc_blocks = 4
    The filesystem on /dev/vdc is now 7864320 (4k) blocks long.
    ```

4.  Run the following command to check whether the file system is expanded:

    ```
    lsblk /dev/vdc
    ```

    Expected output:

    ```
    NAME MAJ:MIN  RM SIZE RO TYPE MOUNTPOINT
    vdc  254:32   0  30G  0  disk /var/lib/kubelet/pods/a26d174f-***/volumes/kubernetes.io~csi/d-0jlhaq***/mount
    ```

    The output indicates that the size of the vdc file system is expanded to 30 GiB.


## Method 2: Expand a disk volume with service interruption

You can stop the application by deleting the StatefulSet or set the value of Replica to 0. Then, you can manually expand the disks and restart the application. The following example demonstrates how to expand both disks to 30 GiB.

**Step 1: Delete the pods that are provisioned for the application**

1.  Run the following command to scale the number of pods to 0:

    ```
    kubectl scale sts web --replicas=0
    ```

    Expected output:

    ```
    statefulset.apps/web scaled
    ```

2.  Run the following command to check whether the pods are deleted:

    ```
    kubectl get pod | grep web
    ```

    No output is returned. This indicates that the web application is stopped.


**Step 2: Expand the disks**

1.  Log on to the [ECS console](https://ecs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Storage & Snapshots** \> **Disks**.

3.  Find the disks named d-0jl0j5\*\*\* and d-0jlhaq\*\*\* and check the states of the disks. Then, choose **More** \> **Resize Disk** in the **Actions** column for each disk.

4.  On the Resize Disks page, select a method to resize the disk, and specify the size to which you want to expand the disk.

    -   If the disk is in the **Unattached** state, do not select **Online Resizing** on the Resize Disks page. Specify the size to which you want to expand the disk in the Size after Resize section. In this example, set the value to 30 GiB.
    -   If the disk is in the **In Use** state, select **Online Resizing** on the Resize Disks page, and specify the size to which you want to expand the disk in the Size after Resize section.
    **Note:** The specified value cannot be smaller than the current disk size.

5.  Confirm the disk expansion fee. Read and select ECS Service Terms, and then click **Confirm**.

    For more information, see [Resize disks online for Linux instances](/intl.en-US/Block Storage/Resize cloud disks/Resize disks online for Linux instances.md).


**Step 3: Expand the file systems**

After the disks are expanded, you must expand the file systems. Otherwise, the storage that the application can use is still 20 GiB.

**Note:** This step is intended for unpartitioned disks that are used in Kubernetes. We recommend that you do not use partitioned disks in Kubernetes.

-   If an unpartitioned disk is mounted as a PV, you cannot manually create partitions for the disk. Otherwise, the file system may be damaged and data loss may occur.
-   If a partitioned disk is mounted as a PV, you must expand the file system after you can expand the partitioned disk. For more information, see [Step 3: View the disk partitions](/intl.en-US/Block Storage/Resize cloud disks/Resize disks online for Linux instances.md) and [Step 4: Resize partitions](/intl.en-US/Block Storage/Resize cloud disks/Resize disks online for Linux instances.mdsection_kmu_sxr_8jk).

1.  Mount the disks to an ECS instance.

    **Note:** You must mount the disks to an ECS instance before you can expand the file systems.

    1.  Log on to the [ECS console](https://ecs.console.aliyun.com).

    2.  In the left-side navigation pane, choose **Storage & Snapshots** \> **Disks**.

    3.  Find the disk that is in the **Unattached** state and choose **More** \> **Attach** in the **Actions** column.

    4.  In the Attach Disk dialog box, select an ECS instance from the drop-down list, and configure the settings that correspond to releasing disks.

        |Parameter|Description|
        |---------|-----------|
        |Target Instance|Select the ECS instance to which you want to mount the disk.|
        |Release Disk with Instance|If you select this option, the disk is automatically released when the ECS instance to which it is mounted is released. If you do not select this option, the disk is retained when the ECS instance to which it is mounted is released. **Note:** If the disk that you want to mount is a system disk that you unmounted from another ECS instance, the ECS instance specified by **Release Disk with Instance** refers to the ECS instance from which you unmounted the disk. |
        |Delete Automatic Snapshots While Releasing Disk|If you select this option, snapshots that are automatically created for the disk are released together with disk. To retain the snapshots, do not select this option.|

    5.  Click **Attach**.

    If the status of the disk becomes **In Use**, the disk is attached.

2.  Connect to the ECS instance to which the disk is mounted and obtain the driver letter of the disk.

    For more information about how to connect to an ECS instance, see [Methods used to connect to ECS instances](/intl.en-US/Instance/Connect to instances/Overview.md).

    -   Run the following command to obtain the driver letter of the disk:

        ```
        for device in `ls /sys/block | grep vd`; do 
          cat /sys/block/$device/serial | grep 0jlhaq*** && echo $device; 
        done
        ```

        **Note:** The ID of the expanded disk is `d-0jlhaq***`. `0jlhaq***` is the string that follows `d-`.

    -   If you cannot obtain the driver letter of the disk by running the preceding command, perform the following operations:
        1.  Unmount the disk and run the `ls /dev/vd*` command to query the list of disks.
        2.  Mount the disk and run the `ls /dev/vd*` command to query the list of disks.
        3.  Compare the lists that are returned. The disk that appears only in the second list is the one that you mounted.
3.  Run the following command to expand the file system:

    ```
    resize2fs /dev/vdb
    ```

    **Note:** /dev/vdc is the driver letter of the disk obtained in the [Step 2](#step_3q5_c6f_yyn).

    Expected output:

    ```
    resize2fs 1.43.5 (04-Aug-2017)
    Resizing the filesystem on /dev/vdb to 7864320 (4k) blocks.
    The filesystem on /dev/vdb is now 7864320 (4k) blocks long.
    ```

4.  Check whether the file system is expanded.

    1.  Run the following command to create a temporary folder named /mnt/disk/ and mount the disk to the folder:

        ```
        mkdir /mnt/disk
        mount /dev/vdb /mnt/disk/
        ```

    2.  Run the following command to query the size of the specified file system:

        ```
        df /mnt/disk/
        ```

        Expected output:

        ```
        Filesystem     1K-blocks  Used Available Use% Mounted on
        /dev/vdb        30832548 45036  30771128   1% /mnt/disk
        ```

        The output indicates that the /dev/vdb folder can use at most 30 GiB of storage. This indicates that the file system is expanded.

    3.  Run the following command to unmount the disk from the temporary folder:

        ```
        umount /mnt/disk
        ```


**Step 4: Restart the application**

1.  Run the following command to scale the number of pods to 2:

    ```
    kubectl scale sts web --replicas=2
    ```

    Expected output:

    ```
    statefulset.apps/web scaled
    ```

2.  Run the following command to check whether the pods are deleted:

    ```
    kubectl get pod | grep web
    ```

    Expected output:

    ```
    NAME         READY   STATUS    RESTARTS   AGE
    web-0        1/1     Running   0          74s
    web-1        1/1     Running   0          42s
    ```

3.  Run the following command to query the size of the specified file system:

    ```
    kubectl exec web-0 df /data
    ```

    Expected output:

    ```
    Filesystem     1K-blocks  Used Available Use% Mounted on
    /dev/vdb        30832548 45036  30771128   1% /data
    ```

    The output indicates that the size of the /dev/vdb file system is expanded to 30 GiB.


## FAQ

Issue:

What can I do if the following message appears after I run the `resize2fs` command?

```
resize of device /dev/xxx failed: exit status 1 resize2fs output: resize2fs xxx(version)
Please run `e2fsck -f /dev/xxx` first
```

Cause:

The file systems are not consistent, which causes I/O errors.

Solution:

Run the `e2fsck -f /dev/xxx` command and then expand the file systems.

**Related topics**  


[Automatically expand a disk volume](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Disk volumes/Automatically expand a disk volume.md)

