# Set the node autonomy attribute

This topic introduces how to set the autonomy attribute for edge nodes.

-   A managed edge cluster of Container Service for Kubernetes \(ACK\) is created. For more information, see [Create a managed edge cluster](/intl.en-US/User Guide for Edge Container Service/Edge cluster management/Create a managed edge cluster.md).
-   An edge node is added. For more information, see [Add an edge node](/intl.en-US/User Guide for Edge Container Service/Node management/Add an edge node.md).

You can change the node autonomy attribute in the ACK console. Nodes can be in the autonomous or non-autonomous state. If an autonomous node is disconnected from a master node, the master node prevents the management of the autonomous node and avoids evictions of pods from the autonomous node. This feature applies to edge computing where the network connection is not optimized.

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Nodes**.

5.  On the **Nodes** page, find the node that you want to manage and choose **More** \> **Node Autonomy Setting** in the **Actions** column of the node.

6.  In the **Node Autonomy Setting** message, click **OK**.

    ![Set the node autonomy attribute](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3735359951/p54051.png)

    **Note:** In this example, the autonomous state is enabled for the node. You can perform the preceding steps to disable the autonomous state for the node.


