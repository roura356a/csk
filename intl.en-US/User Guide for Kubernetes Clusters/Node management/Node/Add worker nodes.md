# Add worker nodes

If the workload of your cluster increases, the cluster resources may be insufficient. In this case, we recommend that you add resources to your cluster. For example, if your cluster contains more than 10 nodes, you can add worker nodes to improve resource utilization and reduce O&M complexity. This topic describes how to add worker nodes to a cluster.

A Kubernetes cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  Purchase new nodes or add existing nodes as needed.

    -   Perform the following steps to purchase new nodes and add them to the cluster. For more information, see [Expand an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Expand an ACK cluster.md).
        1.  Find the cluster to which you want to add nodes, and choose **More** \> **Expand** in the **Actions** column.
        2.  Configure the new worker nodes.

            ![Expand a cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5771279951/p50574.png)

        3.  Set the number of worker nodes to add.

            ![Expand a cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5771279951/p10905.png)

        4.  Set the logon type.

            ![Expand a cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3246858951/p9041.png)

        5.  Set RDS Whitelist.

            ![Expand a cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4246858951/p21270.png)

        6.  Attach labels to new nodes.

            ![Expand a cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4246858951/p50326.png)

        7.  After the configuration is complete, click **Submit**.
    -   To add existing nodes to a cluster, perform the following steps:
        1.  Find the cluster to which you want to add nodes, and choose **More** \> **Add Existing Node** in the **Actions** column.

            On the Add Existing ECS Instance page, you can select the **Auto** or **Manual** mode to add ECS instances. For more information, see [Add existing ECS instances to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node/Add existing ECS instances to an ACK cluster.md). In this example, the Auto mode is selected.

        2.  Set Mode to **Auto** and select instances from the ECS instance list. Then click **Next Step**.

            ![Auto mode](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6635359951/p10826.png)

        3.  Specify instance information, including the CPU policy, logon password, and labels, and click **Next Step**.

            ![Instance information](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6635359951/p10827.png)

        4.  In the message that appears, click **Confirm**. The selected ECS instances are automatically added to the cluster.

            ![ ECS instances](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6635359951/p10828.png)

4.  On the management page of the cluster, click **Nodes** in the left-side navigation pane to view the status of the added nodes.

    If the added nodes are in the **Running** state, the operation is successful.


To remove a node from a cluster, find the node that you want to remove on the Nodes page, and choose **More** \> **Remove** in the **Actions** column. In the message that appears, click **OK**.

**Note:**

-   To ensure the stability of your applications, we recommend that you add nodes to the cluster before you remove unnecessary nodes.
-   You can also configure [PodDisruptionBudget \(PDB\) objects](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) to ensure application stability when you remove nodes.

