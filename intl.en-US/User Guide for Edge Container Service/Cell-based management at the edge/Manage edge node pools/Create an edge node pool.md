---
keyword: create an edge node pool
---

# Create an edge node pool

An edge node pool manages a group of nodes in a cluster, including the labels and taints of the nodes. This topic describes how to create an edge node pool in the Container Service for Kubernetes \(ACK\) console.

-   A managed edge Kubernetes cluster is created. For more information, see [Create a managed edge Kubernetes cluster](/intl.en-US/User Guide for Edge Container Service/Edge cluster management/Create a managed edge Kubernetes cluster.md).
-   The version of your cluster is 1.16 or later.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.

5.  On the Node Pools page, click **Create Edge Node Pool \(Beta\)** in the upper-right corner of the page.

6.  In the **Create Edge Node Pool \(Beta\)** dialog box, set the parameters and click **Submit**.

    |Parameter|Description|
    |---------|-----------|
    |Name|The name of the edge node pool.|
    |Coordination Network between Cloud and Edge|You can select Basic or Enhanced. For more information, see [Create an enhanced edge node pool \(public preview\)]().|
    |Maximum Nodes|The maximum number of nodes that can be added to the edge node pool.|
    |Node Label|You can add labels to the nodes in the edge node pool.|
    |Taints|You can add taints to the nodes in the edge node pool.|

    After the edge node pool is created, you can view information about the created node pool in the node pool list.


**Related topics**  


[Overview of edge node pools](/intl.en-US/User Guide for Edge Container Service/Cell-based management at the edge/Manage edge node pools/Overview of edge node pools.md)

[Create a managed edge Kubernetes cluster](/intl.en-US/User Guide for Edge Container Service/Edge cluster management/Create a managed edge Kubernetes cluster.md)

[Add nodes to an edge node pool]()

