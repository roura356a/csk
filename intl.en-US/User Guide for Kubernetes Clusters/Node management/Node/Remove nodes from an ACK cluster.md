# Remove nodes from an ACK cluster

This topic describes how to remove nodes from a cluster of Container Service for Kubernetes \(ACK\).

-   [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)
-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md)

-   When you remove nodes from an ACK cluster, you may need to migrate pods that run on these nodes to other nodes. This may cause service interruption. We recommend that you remove nodes during off-peak hours.
-   Unknown errors may occur when you remove nodes. We recommend that you back up the data on these nodes before you remove the nodes.
-   Nodes remain in the unschedulable state when they are being removed.
-   Only worker nodes can be removed.
-   We recommend that you remove nodes in the ACK console. If you run the kubectl delete node command to remove nodes from an ACK cluster, note the following limits:
    -   The removed nodes cannot be added to other ACK clusters.
    -   The Elastic Compute Service \(ECS\) instances are automatically released after the nodes are removed.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the **Nodes** page, find the node that you want to remove and choose **More** \> **Remove** in the **Actions** column for the node.

    **Note:** To remove multiple nodes at a time, select the nodes that you want to remove on the **Nodes** page and click **Batch Remove**.

5.  In the **Remove Node** dialog box, select the **Release ECS Instance** and **Drain the Node** check boxes, and click **OK**.

    After the node is removed, the ECS instance is released. This is the ECS instance where the node is deployed.

    -   **Release ECS Instance**:
        -   Select this option to release only pay-as-you-go ECS instances.
        -   Subscription ECS instances are automatically released after the subscription expires.
        -   If you do not select the **Release ECS Instance** check box, the system continues to bill the ECS instance where the node is deployed.
    -   **Drain the Node**: Select this option to migrate pods that run on the removed nodes to other nodes in the cluster. If you select this option, make sure that the other nodes in the cluster have sufficient resources for these pods.

        You can also run the `kubectl drain node-name` command to migrate pods that run on removed nodes to other nodes in the cluster.

        **Note:** The value of node-name must be in the format of your-region-name.node-id. For example, the value can be cn-hangzhou.i-xxx.

        -   your-region-name specifies the region where the cluster is deployed.
        -   node-id specifies the ID of the ECS instance where the node is deployed.

