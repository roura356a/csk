---
keyword: [edge node pool, add nodes]
---

# Add nodes to an edge node pool

You can add worker nodes to an edge node pool that you created. Make sure that these worker nodes can communicate with the API server. This topic describes how to add nodes to an edge node pool.

-   An edge node pool is created. For more information, see [Create an edge node pool](/intl.en-US/User Guide for Edge Container Service/Cell-based management at the edge/Manage edge node pools/Create an edge node pool.md).
-   The version of your cluster is 1.16 or later.

**Note:** When you use a managed edge Kubernetes cluster, take note of the following limits:

-   You can add only nodes that run CentOS 7.4, CentOS 7.6, or Ubuntu 18.04.
-   Only Edge Node Service \(ENS\) instances with at least 2 cores and 4 GB of memory can be automatically added to the cluster. In addition, the ENS instances must be in the Running state and run CentOS 7.4 or 7.6.
-   If the version of the cluster is 1.14.8-aliyunedge.1 or later, Advanced RISC Machine \(ARM\) nodes that run CentOS 7.4 or ARM64 nodes that run Ubuntu 18.04 can be added to the cluster.

## Add nodes to an edge node pool

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.

5.  On the Node Pools page, find the edge node pool to which you want to add nodes and click **Add Existing Node** in the **Actions** column.

    Follow the steps that are described in the Add an edge node topic to add nodes. The topic also describes how to add ENS instances in auto and manual modes. For more information, see [Add an edge node](/intl.en-US/User Guide for Edge Container Service/Node management/Add an edge node.md).

    After you add nodes to the edge node pool, you can click **Details** in the **Actions** column to view the nodes that you added.


**Related topics**  


[Add an edge node](/intl.en-US/User Guide for Edge Container Service/Node management/Add an edge node.md)

