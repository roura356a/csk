# Mark a node as unschedulable

This topic describes how to mark a node as schedulable or unschedulable in the Container Service for Kubernetes \(ACK\) console. This allows you to optimize the distribution of the loads on each node.

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the **Nodes** page, select the node you want to manage and click **Set to Unschedulable**.

5.  In the **Set to Unschedulable** dialog box, set the scheduling policy.

    -   **Set to Unschedulable**: Pods will not be scheduled to this node when you deploy new applications.
    -   **Set to Unschedulable and Drain**: Pods will not be scheduled to this node when you deploy new applications. Pods on this node will be evicted, except for the pods that are managed by DaemonSet.
6.  Click **OK**.

    The status of the specified node is changed to Unschedulable.


To mark a node as schedulable, select the node and click **Set to Schedulable**. In the message that appears, click **OK**. The status of the specified node is changed to **Schedulable**.

