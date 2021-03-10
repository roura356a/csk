# View node resource request rate and usage

You can view the resource usage on the nodes of a cluster in the Container Service for Kubernetes console.

[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  The Cluster Information page appears. In the left-side navigation pane, click **Nodes**.

    On the **Nodes** page, you can view the request rate and usage of CPU and memory on each node. The following formulas show how to calculate the request rate and usage:

    -   CPU request rate = sum \(The amount of CPU requested by all pods on the node\)/Total CPU of the node
    -   CPU usage rate = sum \(The amount of CPU used by all pods on the node\)/Total CPU of the node
    -   Memory request rate = \(The amount of memory requested by all pods on the node\)/Total memory of the node
    -   Memory usage rate = sum \(The amount of memory used by all pods on the node\)/Total memory of the node
    **Note:**

    -   You can adjust the workload of a node based on resource usage. For more information, see [Mark a node as unschedulable](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node/Mark a node as unschedulable.md).
    -   If both the resource request and usage are 100% on a node, new pods will not be scheduled to this node.

