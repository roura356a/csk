---
keyword: [remove a node, remove a server, K8s, drain a node]
---

# Remove a node

This topic describes how to remove idle nodes from a Container Service for Kubernetes \(ACK\) cluster.

-   An ACK cluster is created. For more information, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).
-   A kubectl client is connected to the cluster. For more information, see [Connect to ACK clusters by using kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md).

## Precautions

-   When you remove a node, the pods that run on the node are migrated to other nodes. This may cause service interruptions. We recommend that you remove nodes during off-peak hours.
-   Unknown errors may occur when you remove nodes. Before you remove nodes, back up the data on the nodes.
-   Nodes remain in the Unschedulable state when they are being removed.
-   You can remove only worker nodes. You cannot remove master nodes.
-   We recommend that you remove nodes in the ACK console. If you run the kubectl delete node command to remove nodes from an ACK cluster, take note of the following limits:
    -   The removed nodes cannot be added to other ACK clusters.
    -   After you delete a cluster, the Elastic Compute Service \(ECS\) instances where the removed nodes are deployed are automatically released.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Nodes**.

5.  On the **Nodes** page, find the node that you want to remove and choose **More** \> **Remove** in the **Actions** column.

    **Note:** To remove multiple nodes at a time, select the nodes that you want to remove on the **Nodes** page and click **Batch Remove**.

6.  In the **Remove Node** dialog box, select **Release ECS Instance** and **Drain the Node**, and then click **OK**.

    -   **Release ECS Instance**:
        -   Only pas-as-you-go ECS instances are released. The system continues to bill ECS instances that are not released.
        -   Subscription ECS instances are automatically released after the subscription expires.
        -   If you do not select **Release ECS Instance**, you are still charged for the ECS instance where the node is deployed.
    -   **Drain the Node**: Select this option to migrate pods that run on the nodes to be removed to other nodes in the cluster. If you select this option, make sure that the other nodes have sufficient resources for these pods.

        You can also run the `kubectl drain node-name` command to migrate pods that run on the nodes to be removed to other nodes in the cluster.

        **Note:**

        -   node-name must be in the format of your-region-name.node-id.

            your-region-name specifies the region where the cluster that you want to manage is deployed. node-id specifies the ID of the ECS instance where the node to be removed is deployed. Example: cn-hangzhou.i-xxx.

        -   `options` specifies the optional parameters of the command. Example: `--force --ignore-daemonsets --delete-local-data`. You can run the `kubectl drain --help` command to view help information.

