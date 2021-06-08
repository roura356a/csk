---
keyword: [edge Kubernetes cluster, node autonomy, edge node is disconnected from the cloud]
---

# Configure node autonomy

This topic describes how to set the autonomy attribute for edge nodes. If an edge node is autonomous, applications run as expected on the edge node even if the edge node is disconnected from the cloud. This ensures that applications are not removed or migrated to other edge nodes in the case of network errors.

-   [t855887.md\#](/intl.en-US/User Guide for Edge Container Service/Edge cluster management/Create a managed edge Kubernetes cluster.md)
-   [t1039919.md\#](/intl.en-US/User Guide for Edge Container Service/Node management/Add an edge node.md)

You can enable or disable node autonomy in the Container Service for Kubernetes \(ACK\) console.

-   If an autonomous edge node is disconnected from the cloud, ACK does not migrate applications on this node to other nodes, and the applications are automatically restored. Node autonomy is applicable to edge computing scenarios where the network connection is weak.
-   If a non-autonomous edge node is disconnected from the cloud, the node fails to send heartbeats to the nodes in the cloud. As a result, the state of the node is changed to **NotReady** and the applications on the node are removed or migrated to other nodes after a specific time period.

1.  On the **Clusters** page, find the cluster that you want to manage, and click the cluster name or click **Details** in the **Actions** column.

2.  On the **Nodes** page, find the node that you want to manage and choose **More** \> **Node Autonomy Setting** in the **Actions** column.

    **Note:** The **Node Autonomy Setting** option is available for only edge nodes.

3.  In the Node Autonomy Setting dialog box, click **OK**.

    **Note:** By default, edge nodes are not autonomous when they are added to the cluster. You can follow the preceding steps to enable or disable node autonomy.


**Related topics**  


[t1886138.md\#](/intl.en-US/User Guide for Edge Container Service/ACK@Edge overview.md)

[t1039919.md\#](/intl.en-US/User Guide for Edge Container Service/Node management/Add an edge node.md)

