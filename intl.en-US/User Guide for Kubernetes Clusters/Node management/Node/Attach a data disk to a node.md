# Attach a data disk to a node

The existing disk capacity may be insufficient to support the increasing number of containers or images on a node in a Kubernetes cluster. You can attach one or more data disks to the node. This allows you to expand the disk capacity and provide more storage space for containers and images. This topic describes how to attach a data disk to a node.

Kubernetes version 1.10.4 or later is used in the cluster.

## Attach a data disk to the node

To expand the disk capacity of an existing node, you can use either of the following methods:

-   If no disk is attached to the node, attach a data disk to the node. For more information, see [Mount a disk to the Docker data directory](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node/Mount a data disk to the Docker data directory.md).
-   If you have purchased a data disk for the node, but the disk has failed to be attached to the node, perform the following steps:

**Note:**

-   To minimize the potential risks, we recommend that you create snapshots or backups of the node.
-   Make sure that the applications deployed on the node can be scheduled to other nodes in the cluster.
-   Perform the steps during off-peak hours.
-   If you drain the node, the pods on this node are scheduled to other nodes. Make sure that the cluster has sufficient nodes. If no sufficient nodes are available, we recommend that you temporarily scale out the nodes before the node is drained.

Before you attach the data disk, run the df command on the worker node. If the output indicates that `/var/lib/docker` is mounted to `/dev/vdb1`, the data disk is attached to the node. Then, you can skip the following steps. If the /var/lib/docker directory is not mounted, perform the following steps:

![Attach a data disk to the node](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7735359951/p38111.png)

1.  Set the node to the unschedulable state.

    For more information, see [Mark node as unschedulable](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands?spm=a2c4e.11153940.blogcont686229.11.474d61a9QLqauh#cordon).

2.  Drain the node.

    For more information, see [Safely-Drain-Node](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/).

3.  Remove the node.

    This topic only describes the operations in the Container Service for Kubernetes console.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Applications** in the **Actions** column.

    4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node**.

    5.  Select a node that you want to remove, and click **Batch Remove**, or choose **More** \> **Remove** in the Actions column of the node.

    6.  In the Remove Node dialog box, click **OK**.

4.  Add the removed node to the cluster.

    1.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node**.

    2.  On the Nodes page, click **Add Existing Node**.

    3.  On the Select Existing ECS Instance wizard page, set Mode to **Auto** or **Manual**. Auto is selected in this example.

    4.  Select the Elastic Compute Service \(ECS\) that you want to add in the Select Existing ECS Instance section and click **Next Step**.

    5.  Follow the prompts as instructed to complete the required settings and click **Next Step**.

    6.  In the **Confirm** message, click **OK**.


After the node is added to the cluster, log on to the node and run the df command to check whether the data disk is attached to the node.

The following figure shows that the data disk is attached to the node.

![Attached data disk](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7735359951/p38111.png)

This way, the data disk is attached to the existing node.

