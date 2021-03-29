---
keyword: Remove edge nodes
---

# Remove edge nodes

You can remove edge nodes from a managed edge Kubernetes cluster. This topic describes how to remove edge nodes.

-   [Create a managed edge Kubernetes cluster](/intl.en-US/User Guide for Edge Container Service/Edge cluster management/Create a managed edge Kubernetes cluster.md)
-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md)

-   When you remove an edge node, the pods that run on the node are migrated to other nodes. This may cause service interruption. We recommend that you remove nodes during off-peak hours.
-   Unknown errors may occur when you remove nodes. We recommend that you back up data on these nodes before you remove them.
-   During the removal process, nodes remain in the Unschedulable state.
-   Only worker nodes can be removed. Master nodes cannot be removed.
-   A managed edge Kubernetes cluster can contain two types of nodes: cloud nodes and edge nodes. You can remove both types of nodes at a time.
-   You must retain at least one cloud node in a managed edge Kubernetes cluster.
-   We recommend that you remove nodes in the Container Service for Kubernetes \(ACK\) console. If you run the `kubectl delete node` command to remove nodes, take note of the following limits:
    -   For cloud nodes:
        -   The removed nodes cannot be added to other clusters.
        -   After you delete a cluster, the Elastic Compute Service \(ECS\) instances where the removed nodes are deployed are automatically released.
    -   For edge nodes: You must run the Reset command in Edgeadm to reset the removed nodes before you can add them to other clusters.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node**.

5.  On the **Nodes** page, find the node that you want to remove and choose **More** \> **Remove** in the **Actions** column.

    **Note:** To remove multiple nodes at a time, select the nodes that you want to remove on the **Nodes** page and click **Batch Remove**.

6.  Optional. In the **Remove Node** dialog box, you can select **Release ECS Instance** and **Drain the Node** if all of the selected nodes are cloud nodes. Then, click **OK**.

    **Note:** If one or more edge nodes are selected, the **Release ECS Instance** and **Drain the Node** check boxes are unavailable in the **Remove Node** dialog box.

    -   Release ECS Instance:
        -   Select this option to release only pay-as-you-go ECS instances.
        -   Subscription ECS instances are automatically released after the subscription expires.
        -   If you do not select Release ECS Instance, the system continues to bill the ECS instances where the removed nodes are deployed.
    -   Drain the Node: Select this option to migrate pods that run on the removed nodes to other nodes in the cluster. If you select this option, make sure that the other nodes in the cluster have sufficient resources for these pods. You can also run the `kubectl drain node-name` command to migrate pods that run on the removed nodes to other nodes in the cluster.

        **Note:** The value of node-name must be in the format of your-region-name.node-id. For example, the value can be cn-hangzhou.i-xxx.

        -   your-region-name specifies the region where the cluster is deployed.
        -   node-id specifies the ID of the ECS instance where the node to be removed is deployed.

