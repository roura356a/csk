---
keyword: [node schedulability, schedulability setting, drain a node]
---

# Set node schedulability

This topic describes how to mark a node as schedulable or unschedulable in the Container Service for Kubernetes \(ACK\) console. This allows you to optimize the distribution of the loads on each node.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Nodes**.

5.  On the **Nodes** page, find the node that you want to manage and perform the following operation in the **Actions** column:

    -   Choose **More** \> **Schedulability Settings**. In the message that appears, click Confirm. This way, pods are no longer scheduled to the specified node when you deploy new applications.
    -   Choose **More** \> **Drain**. In the message that appears, click Confirm. This way, pods are no longer scheduled to the specified node when you deploy new applications. In addition, pods on the specified node are evicted, except for the pods that are managed by DaemonSets.
6.  Click **Confirm**.

    The status of the specified node is changed to Unschedulable.


If you want to resume an unschedulable node, perform the following operations:

1.  Find the node that you want to manage and choose **More** \> **Schedulability Settings** in the **Actions** column.
2.  In the Schedulability Settings message, click **Confirm**.

The status of the node is changed to **Schedulable**.

