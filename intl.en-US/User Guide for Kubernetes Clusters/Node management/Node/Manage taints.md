---
keyword: [add a taint, delete a taint]
---

# Manage taints

Pods repel nodes that have specified taints when pods are scheduled. You can add one or more taints to a node. This topic describes how to add a taint to multiple nodes at a time and delete a taint from a node.

An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

## Add a taint to multiple nodes at a time

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  Go to the **Manage Labels and Taints** page.

    1.  In the left-side navigation pane of the ACK console, click **Clusters** to go to the Clusters page.

    2.  On the Clusters page, find the cluster that you want to manage, and click the name of the cluster or click **Details** in the **Actions** column.

    3.  In the left-side navigation pane of the details page, choose **Nodes** \> **Nodes**.

    4.  On the Nodes page, click **Manage Labels and Taints** in the upper-right corner of the page.

3.  Click the **Taints** tab, select one or more nodes, and then click **Add Taint**.

4.  In the dialog box that appears, set **Name**, **Value**, and **Effect**.

5.  Click **OK**.

    On the **Taints** tab, you can verify that the taint is added to the selected nodes.


## Filter nodes by taint

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  Go to the **Manage Labels and Taints** page.

    1.  In the left-side navigation pane of the ACK console, click **Clusters** to go to the Clusters page.

    2.  On the Clusters page, find the cluster that you want to manage, and click the name of the cluster or click **Details** in the **Actions** column.

    3.  In the left-side navigation pane of the details page, choose **Nodes** \> **Nodes**.

    4.  On the Nodes page, click **Manage Labels and Taints** in the upper-right corner of the page.

3.  Click the **Taints** tab and click a taint in the Taints column to filter the nodes.

    The page automatically refreshes and displays the nodes that have the specified taint.

    ![Filter nodes by taint](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2172862061/p129874.png)


## Delete a taint

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  Go to the **Manage Labels and Taints** page.

    1.  In the left-side navigation pane of the ACK console, click **Clusters** to go to the Clusters page.

    2.  On the Clusters page, find the cluster that you want to manage, and click the name of the cluster or click **Details** in the **Actions** column.

    3.  In the left-side navigation pane of the details page, choose **Nodes** \> **Nodes**.

    4.  On the Nodes page, click **Manage Labels and Taints** in the upper-right corner of the page.

3.  On the Manage Labels and Taints page, click the **Taints** tab, select a node, and then click ![Delete icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1381712261/p131930.png) of a taint in the Taints column. In the message that appears, click **Confirm**.

    After the taint is deleted, it disappears from the Taints column.


