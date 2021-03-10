# Manage node labels

You can manage node labels in the console. You can add a label to multiple nodes at the same time, filter nodes by label, and delete labels.

A Kubernetes cluster is created. For more information, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).

You can use labels to schedule nodes. For more information, see [Mark a node as unschedulable](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Mark a node as unschedulable.md).

## Add a label to multiple nodes at the same time

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  To go to the **Manage Labels and Taints** page, perform the following steps:

    1.  In the left-side navigation pane, click **Clusters** to go to the Clusters page.

    2.  On the Clusters page, find the cluster that you want to manage, and click the cluster name, or click **Details** in the **Actions** column of the cluster. The Cluster Information page appears.

    3.  In the left-side navigation pane, click **Nodes**. On the Nodes page, click **Manage Labels and Taints**.

    -   To go to the **Clusters** page and open the **Manage Labels and Taints** page, perform the following steps:
        1.  In the left-side navigation pane, choose **Clusters** \> **Clusters**.
        2.  On the Clusters page, find the cluster that you want to manage and click **Manage** in the **Actions** column of the cluster. The Cluster Information page appears. In the left-side navigation pane, click Nodes. On the Nodes page, click Manage Labels and Taints.
    -   To go to the **Nodes** page and open the **Manage Labels and Taints** page, perform the following steps:
        1.  In the left-side navigation pane, choose **Clusters** \> **Nodes**.
        2.  On the Nodes page, click **Manage Labels and Taints**.
3.  The **Labels** tab appears. Select multiple nodes and click **Add Label**.

4.  In the **Add** dialog box, Set the **Name** and **Value** parameters and click **OK**.

    On the **Labels** tab, you can find that the label is added to the selected nodes.


## Filter nodes by label

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  To go to the **Manage Labels and Taints** page, perform the following steps:

    1.  In the left-side navigation pane, click **Clusters** to go to the Clusters page.

    2.  On the Clusters page, find the cluster that you want to manage, and click the cluster name, or click **Details** in the **Actions** column of the cluster. The Cluster Information page appears.

    3.  In the left-side navigation pane, click **Nodes**. On the Nodes page, click **Manage Labels and Taints**.

    -   To go to the **Clusters** page and open the **Manage Labels and Taints** page, perform the following steps:
        1.  In the left-side navigation pane, choose **Clusters** \> **Clusters**.
        2.  On the Clusters page, find the cluster that you want to manage and click **Manage** in the **Actions** column of the cluster. The Cluster Information page appears. In the left-side navigation pane, click Nodes. On the Nodes page, click Manage Labels and Taints.
    -   To go to the **Nodes** page and open the **Manage Labels and Taints** page, perform the following steps:
        1.  In the left-side navigation pane, choose **Clusters** \> **Nodes**.
        2.  On the Nodes page, click **Manage Labels and Taints**.
3.  Click the **Labels** tab and click the label that you want to filter in the Labels column.

    The page automatically refreshes and displays nodes that have the label.

    ![Filter nodes](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0735359951/p10915.png)


## Delete a label

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  To go to the **Manage Labels and Taints** page, perform the following steps:

    1.  In the left-side navigation pane, click **Clusters** to go to the Clusters page.

    2.  On the Clusters page, find the cluster that you want to manage, and click the cluster name, or click **Details** in the **Actions** column of the cluster. The Cluster Information page appears.

    3.  In the left-side navigation pane, click **Nodes**. On the Nodes page, click **Manage Labels and Taints**.

    -   To go to the **Clusters** page and open the **Manage Labels and Taints** page, perform the following steps:
        1.  In the left-side navigation pane, choose **Clusters** \> **Clusters**.
        2.  On the Clusters page, find the cluster that you want to manage and click **Manage** in the **Actions** column of the cluster. The Cluster Information page appears. In the left-side navigation pane, click Nodes. On the Nodes page, click Manage Labels and Taints.
    -   To go to the **Nodes** page and open the **Manage Labels and Taints** page, perform the following steps:
        1.  In the left-side navigation pane, choose **Clusters** \> **Nodes**.
        2.  On the Nodes page, click **Manage Labels and Taints**.
3.  Click the **Labels** tab, select the node that you want to manage, and then click the Delete icon to delete the label.

    The specified label is deleted.


