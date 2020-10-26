# Manage labels

You can manage labels on the nodes through the console. You can add labels to multiple nodes at the same time, filter nodes by label, and delete nodes.

You have created a Kubernetes cluster. For more information, see [Create a cluster of ACK Proprietary Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Proprietary Edition.md).

For more information about how to use labels to schedule Pods, see [Mark a node as unschedulable](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Mark a node as schedulable or unschedulable.md).

## Add labels to multiple nodes

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  Select the cluster and click **Manage Labels** in the upper-right corner.

    ![Manage labels](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/9635359951/p10910.png)

3.  Select one or multiple nodes and click **Add Label**.

    ![Add labels to multiple nodes](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0735359951/p10911.png)

4.  In the dialog box that appears, enter the name and value of the label and click **OK**.

    ![Add labels](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0735359951/p10912.png)

    On the Manage Labels page, you can find that multiple nodes have the same label.

    ![View labels](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0735359951/p10913.png)


## Filter nodes by label

1.  Select the cluster and click **Manage Labels** in the upper-right corner.

    ![Manage labels](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0735359951/p10914.png)

2.  Choose a node and click a label in the Label column to filter nodes by this label. In this example, click `group:worker`.

    You can then view nodes with the `group:worker` label.

    ![Filter nodes](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0735359951/p10915.png)


## Delete labels

1.  Select the cluster and click **Manage Labels** in the upper-right corner.

    ![Manage labels](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0735359951/p10916.png)

2.  Select a node and click the delete icon in the Label column to delete a label. In this example, choose `group:worker`.

    ![The delete icon](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0735359951/p10917.png)

    The specified label is deleted.

    ![Delete a label](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0735359951/p10918.png)


