# Remove a node {#task_tqk_v54_dgb .task}

Before you restart or release an ECS instance in a Kubernetes cluster, you need to remove the ECS node from the cluster. This topic describes how to remove a node from a Kubernetes cluster.

-   You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   You have connected to the Kubernetes cluster by using kubectl, see [Connect to a Kubernetes cluster by using kubectl](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#).

-   Removing a node causes pod migration. This may affect the services provided by the pods running on the node. Therefore, we recommend that you remove a node only when fewer services are in demand.
-   Removing a node may cause unintended risks. We recommend that you back up your data in advance and exercise caution when performing this action.
-   Only Worker nodes can be removed.

1.  Run the following command to migrate the pods on the target node to other nodes: 

    **Note:** You must ensure that other nodes in the Kubernetes cluster have sufficient resources to run the pods that you want to migrate.

    ```
    kubectl drain node-name
    ```

    **Note:** The node-name parameter must be in the format of your-region-name.node-id.

    -   your-region-name indicates the name of the region where your cluster resides.
    -   node-id indicates the ID of the ECS instance in which the node to be removed resides. For example, cn-hanghzou.i-xxx.
2.  Set the node to be removed as the non-schedulable node. 

    **Method 1: Use a command**

    -   Run the following command to set the node to be removed as the non-schedulable node:

        ```
        kubectl cordon node-name
        ```

    **Method 2: Use the Container Service console**

    For more information, see [Set node scheduling](reseller.en-US/User Guide/Kubernetes cluster/Nodes/Set node scheduling.md#).

3.  In the left-side navigation pane under Kubernetes, choose **Clusters** \> **Nodes**. 
4.   Under the target cluster, select the target node, and choose **More** \> **Remove** in the Action column.![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/79987/154528754734358_en-US.png)

 

    **Note:** If you want to remove multiple nodes at a time, you can select the target cluster on the Node List page, select all the nodes to be removed, and then click **Remove**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/79987/154528754734360_en-US.png)

5.   Select the **Release ECS at the Same Time** check box to permanently release the ECS instance where the node resides.![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/79987/154528754734359_en-US.png)

 

    **Note:** 

    -   Only Pay-As-You-Go ECS instances can be released.
    -   A Subscription ECS instance will be released automatically when it expires.
    -   Before an ECS instance expires, you can:
        -   Release the ECS instance and apply for a refund.
        -   Change its billing method to Pay-As-You-Go and then release the ECS instance.
    -   If you do not select the **Release ECS at the Same Time** check box, the ECS instance in which the node resides will continue to be charged.
6.  Click **OK**. 

