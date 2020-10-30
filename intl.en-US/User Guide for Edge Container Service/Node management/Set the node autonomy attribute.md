---
keyword: [edge cluster, node autonomy, edge node is disconnected from the cloud]
---

# Set the node autonomy attribute

This topic describes how to set the autonomy attribute for edge nodes. If an edge node is autonomous, applications on the edge node can run properly even if the edge node is disconnected from the cloud. This ensures that applications are not removed or migrated to other edge nodes in case of a network error.

-   [Create a managed edge cluster](/intl.en-US/User Guide for Edge Container Service/Edge cluster management/Create a managed edge cluster.md)
-   [Add an edge node](/intl.en-US/User Guide for Edge Container Service/Node management/Add an edge node.md)

You can enable or disable node autonomy in the Container Service for Kubernetes \(ACK\) console.

-   If an autonomous edge node disconnects from the cloud, ACK does not migrate applications on the node to other nodes, and the applications are automatically restored. Node autonomy is applicable to edge computing scenarios where the network connection is weak.
-   If a non-autonomous edge node is disconnected from the cloud, the node fails to send heartbeats to cloud management nodes and the state of the node changes to **NotReady**. The applications on the node are removed or migrated to other nodes after a specified time period.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the **Clusters** page, find the cluster for which you want to enable autonomy and click **Details** in the **Actions** column.

4.  In the left-side navigation pane, click **Nodes**.

5.  On the **Nodes** page, find the node for which you want to enable autonomy and choose **More** \> **Node Autonomy Setting** in the Actions column.

    **Note:** The **Node Autonomy Setting** option is available for only edge nodes.

6.  In the Node Autonomy Setting dialog box, click **OK**.

    **Note:** By default, edge nodes that are newly added to the cluster are not autonomous. You can follow the preceding steps to enable or disable node autonomy.


**Related topics**  


[t1884276.md\#](t1884276.md#)

[Add an edge node](/intl.en-US/User Guide for Edge Container Service/Node management/Add an edge node.md)

