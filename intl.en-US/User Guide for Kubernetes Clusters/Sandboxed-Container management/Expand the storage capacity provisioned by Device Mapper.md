---
keyword: [Device Mapper, expand the storage capacity, nodes that run sandboxed containers]
---

# Expand the storage capacity provisioned by Device Mapper

If you require more disk space, you can expand the storage capacity provisioned by Device Mapper. This topic describes how to expand the disk space provisioned by Device Mapper on a node that runs sandboxed containers.

[Create a managed ACK cluster that supports sandboxed containers](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Create a managed ACK cluster that supports sandboxed containers.md)

Device Mapper is used to store the root file systems \(RootFS\) of sandboxed containers. When you configure a worker node that runs sandboxed containers, you may find it difficult to estimate the data disk size that you actually require. Insufficient disk space limits the number of pods that can be created on the node.

Device Mapper stores all RootFS of sandboxed containers on a node. The disk space provisioned by Device Mapper is the same as that of the node. Logical Volume Manager \(LVM\) is a tool for the management of disk space provisioned by Device Mapper. You can run LVM commands to expand the disk space provisioned by Device Mapper.

**Note:** This topic is applicable only to Sandboxed-Container V1 \(V1.X.X\). Sandboxed-Container V2 is not supported.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of the target cluster or click **Details** in the **Actions** column.

4.  In the left-side navigation pane, click **Nodes**.

5.  On the Nodes page, click the ID of the ECS instance that you want to expand. The **details** page of the ECS instance appears. In the left-side navigation pane, click **Disks**.

6.  On the **Disks** page, find the data disk and select **Resize Disk** from the **More** drop-down list in the **Actions** column.

7.  On the **Resize Disks** page, select **Online Resizing** for **Resizing Method**, set Size after Resize, select **ECS Service Terms**, and then click **Confirm**.

    In this topic, the disk is expanded from 200 GiB to 500 GiB.

8.  After the expansion is complete, log on to the expanded node and run the `lsblk` command to check whether the disk is expanded.

    For more information about how to log on to an ECS instance, see [ECS connection methods](/intl.en-US/Instance/Connect to instances/Overview.md).

    The output shows that the vdb disk is now expanded from 200 GiB to 500 GiB, as shown in the following figure. The capacity of the logical volume \(LV\) is still 191 GiB, among which 9 GiB is reserved for the volume group \(VG\) and the rest for storing LVM metadata.

    ![result](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0765359951/p95844.png)

9.  Expand the LV

    The VG is automatically expanded after the PV is expanded. Run the following command to expand the persistent volume \(PV\) that uses the LV.

    ```
    pvresize ${the mount path of the disk}
    ```

    The following output is returned:

    ```
    pvresize /dev/vdb
    ```

    Run the following command to expand the LV:

    ```
    lvextend
    ```

    The following output is returned:

    ```
    lvextend -l +100%FREE vg0/mythinpool
    ```

    The output shows that the LV is expanded to 500 GiB.

    ![resize](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1765359951/p95849.png)


