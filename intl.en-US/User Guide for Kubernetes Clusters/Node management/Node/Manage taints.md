---
keyword: [add taints, delete taints]
---

# Manage taints

Pods repel nodes that have specified taints when the pods are scheduled. You can add one or more taints to each node. This topic describes how to add a taint to multiple nodes at a time and delete a taint from a node.

[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)

## Add a taint to multiple nodes

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

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
3.  Click the **Taints** tab, select one or more nodes, and then click **Add Taint**.

4.  In the dialog box that appears, specify the name, value, and effect for the taint.

5.  Click **OK**.

    On the **Taints** tab, you can find that the taint is added to the selected nodes.


## Filter nodes by taints

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

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
3.  Click the **Taints** tab and click a taint in the Taints column.

    The page automatically refreshes and displays nodes that have the taint.

    ![Filter nodes](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2172862061/p129874.png)


## Delete a taint

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

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
3.  On the Manage Labels and Taints page, click the **Taints** tab, select a node, and then click the **Delete** icon. In the message that appears, click **OK**.

    The page automatically refreshes and you can find that the taint is removed from the Taints column.


