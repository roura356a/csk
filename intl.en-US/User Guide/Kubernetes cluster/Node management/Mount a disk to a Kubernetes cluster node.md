# Mount a disk to a Kubernetes cluster node {#concept_s4z_51h_4gb .concept}

This topic describes how to mount a disk to a Kubernetes cluster node. Mounting a disk allows you to expand the Docker data directory and maintain a sufficient disk capacity when the number of containers or images that run on a node increases.

## Prerequisites {#section_kdq_k11_rgb .section}

Your Kubernetes cluster version must be v1.10.4 or later.

You can mount a disk to an existing Kubernetes cluster node by using either of the following methods:

-   If no disk is mounted to the existing node, see [Mount a disk to the Docker data directory](reseller.en-US/User Guide/Kubernetes cluster/Node management/Mount a disk to the Docker data directory.md#).
-   If you have created a disk for the existing node, but you have failed to mount the disk to the node, you can follow these steps.

**Note:** 

-   We recommend that you create a snapshot of the target node or back up node data to avoid data loss.
-   Additionally, you must ensure that you can schedule your cluster applications to other nodes.
-   We recommend that you perform this operation during off-peak service hours to avoid disruptions to your business.
-   Draining a node reschedules pods on the node to other nodes. Therefore, you must ensure that your Kubernetes cluster has sufficient nodes. We recommend that you add cluster nodes in advance as needed.

Before performing the operation, you need to determine whether a disk is already mounted to the target cluster node. To do so, run the df command on the target Worker node, and then check whether `/var/lib/docker` has been mounted to `/dev/vdb1`. If the disk mounting operation failed, you can mount the disk by following these steps.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/119756/155019579038108_en-US.png)

1.  Set the target node as unschedulable. For more information, see [Mark node as unschedulable](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands?spm=a2c4e.11153940.blogcont686229.11.474d61a9QLqauh#cordon).
2.  Drain the target node. For more information, see [Safely drain a node](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/).
3.  Remove the target node. This topic uses the Container Service console as an example.
    1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
    2.  In the left-side navigation pane, click **Node**.
    3.  Select the target node, and click **Remove** or choose **More** \> **Remove**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/119756/155019579038117_en-US.png)

    4.  In the displayed Remove Node dialog box, click **OK**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/119756/155019579938118_en-US.png)

        **Note:** We recommend that you do not select the **Release ECS at the same time** check box. Otherwise, the ECS instance used by the target node will be released.

4.  Add the removed node to the cluster.
    1.  In the left-side navigation pane, click **Clusters**.
    2.  On the right of the target cluster, choose **More** \> **Add Existing Instance**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/119756/155019579938112_en-US.png)

    3.  Select **Automatically Add** or **Manually Add**. In this example, the instance is added automatically. 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/119756/155019579938113_en-US.png)

    4.  Select the existing instance and then click **Next Step**.
    5.  Turn on the **Format Data Disk** switch.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/119756/155019579938114_en-US.png)

    6.  Complete other required settings.

After the node has been added to the cluster, you can log on to the node to run the df command to check whether a disk has been mounted to the target node.

The following figure shows the disk has been amounted to the target node.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/119756/155019579938111_en-US.png)

