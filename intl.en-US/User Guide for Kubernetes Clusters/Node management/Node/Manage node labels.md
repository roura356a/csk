---
keyword: [node labels, add a label to multiple nodes at a time, delete, filter]
---

# Manage node labels

You can manage node labels in the Container Service for Kubernetes \(ACK\) console. You can add a label to multiple nodes at a time, filter nodes by label, and delete labels.

An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

You can use labels to schedule nodes. For more information, see [Mark a node as unschedulable](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node/Mark a node as unschedulable.md).

## Add a label to multiple nodes at a time

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  Go to the **Manage Labels and Taints** page.

    1.  In the left-side navigation pane of the ACK console, click **Clusters** to go to the Clusters page.

    2.  On the Clusters page, find the cluster that you want to manage, and click the name of the cluster or click **Details** in the **Actions** column.

    3.  In the left-side navigation pane of the details page, choose **Nodes** \> **Nodes**.

    4.  On the Nodes page, click **Manage Labels and Taints** in the upper-right corner of the page.

3.  Click the **Labels** tab. Select multiple nodes and click **Add Label**.

4.  In the **Add** dialog box, set **Name** and **Value**, and then click **OK**.

    On the **Labels** tab, you can verify that the label is added to the selected nodes.


## Filter nodes by label

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  Go to the **Manage Labels and Taints** page.

    1.  In the left-side navigation pane of the ACK console, click **Clusters** to go to the Clusters page.

    2.  On the Clusters page, find the cluster that you want to manage, and click the name of the cluster or click **Details** in the **Actions** column.

    3.  In the left-side navigation pane of the details page, choose **Nodes** \> **Nodes**.

    4.  On the Nodes page, click **Manage Labels and Taints** in the upper-right corner of the page.

3.  Click the **Labels** tab and click a label in the Labels column to filter the nodes.

    The page automatically refreshes and displays the nodes that have the specified label.

    ![Filter nodes by label](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0735359951/p10915.png)


## Delete a label

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  Go to the **Manage Labels and Taints** page.

    1.  In the left-side navigation pane of the ACK console, click **Clusters** to go to the Clusters page.

    2.  On the Clusters page, find the cluster that you want to manage, and click the name of the cluster or click **Details** in the **Actions** column.

    3.  In the left-side navigation pane of the details page, choose **Nodes** \> **Nodes**.

    4.  On the Nodes page, click **Manage Labels and Taints** in the upper-right corner of the page.

3.  Click the **Labels** tab, select a node, and then click the Delete icon of a label in the Labels column to delete the label.

    After the label is deleted, it disappears from the Labels column.


