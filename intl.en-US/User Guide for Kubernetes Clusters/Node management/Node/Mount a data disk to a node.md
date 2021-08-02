---
keyword: [mount a data disk, cluster node]
---

# Mount a data disk to a node

The existing disk capacity may be insufficient to support the increasing number of containers or images on a node in a Kubernetes cluster. You can attach one or more data disks to the node. In this case, you must expand the disk capacity to provide more storage space for containers and images. This topic describes how to mount a data disk to a node.

To mount a data disk, make sure that your cluster runs Kubernetes 1.10.4 or later.

## Mount a data disk

You can use one of the following methods to expand the disk capacity of an existing node:

-   If no disk is attached to the node, attach a data disk to the node. For more information, see [Mount a disk to the Docker data directory](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node/Mount a data disk to the Docker data directory.md).
-   If you have purchased a data disk for the node, but the disk fails to be attached to the node, perform the following steps:

**Note:**

-   To minimize the potential risks, we recommend that you create snapshots or backups of the node.
-   Make sure that the applications deployed on the node can be scheduled to other nodes in the cluster.
-   Perform the steps during off-peak hours.
-   If you drain the node, the pods on this node are scheduled to other nodes. Make sure that the cluster has sufficient nodes. If no sufficient nodes are available, we recommend that you temporarily scale out the nodes before the node is drained.

Before you attach the data disk, run the df command on the node. If the output indicates that `/var/lib/docker` is mounted to `/dev/vdb1`, the data disk is attached to the node. Then, you can skip the following steps. If the /var/lib/docker directory is not mounted, perform the following steps:

![Mount a data disk](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/7735359951/p38111.png)

1.  Drain the node.

    For more information, see [t17041.dita\#task\_mpc\_1mb\_r2b](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node/Set node schedulability.md).

2.  Remove the node.

    This topic only describes the operations in the Container Service for Kubernetes \(ACK\) console.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Applications** in the **Actions** column.

    4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Nodes**.

    5.  Select the node that you want to remove, and click **Batch Remove**, or choose **More** \> **Remove** in the Actions column.

    6.  In the Remove Node dialog box, click **OK**.

3.  Add the removed node to the cluster.

    1.  In the left-side navigation pane of the details page, choose **Nodes** \> **Nodes**.

    2.  On the Nodes page, click **Add Existing Node**.

    3.  On the Select Existing ECS Instance wizard page, set Mode to **Auto** or **Manual**. Auto is selected in this example.

    4.  Select the Elastic Compute Service \(ECS\) that you want to add in the Select Existing ECS Instance section and click **Next Step**.

    5.  Follow the instructions on the page to complete the required operations. Then, click **Next Step**.

    6.  In the **Confirm** message, click **Confirm**.


After the node is added to the cluster, log on to the node and run the df command to check whether the data disk is attached to the node.

The following figure shows that the data disk is attached to the node.

![Attached data disk](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/7735359951/p38111.png)

This way, the data disk is attached to the existing node.

