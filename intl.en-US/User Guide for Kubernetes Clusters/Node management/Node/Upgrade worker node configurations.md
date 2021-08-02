---
keyword: [worker node, upgrade resource configurations]
---

# Upgrade worker node configurations

If the workload of your cluster increases, the resources of the cluster may become insufficient to meet the requirements. In this case, you can expand the cluster by adding computing resources. For example, if your cluster contains more than 10 nodes, you can upgrade the configurations of worker nodes. This improves resource utilization and reduces O&M complexity. This topic describes how to upgrade the configurations of worker nodes in a Container Service for Kubernetes \(ACK\) cluster.

An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Nodes**.

5.  Click the ID of the worker node for which you want to upgrade the configurations to go to the instance details page. In this example, the worker-k8s-for-cs instance is used.

    On the instance details page, you can view information about the instance, such as the instance type.

    Pay-as-you-go worker nodes are used in this example. For more information about how to upgrade worker nodes that use other billing methods, see [Overview of instance upgrade and downgrade](/intl.en-US/Instance/Change configurations/Overview of instance upgrade and downgrade.md).

6.  If **Change Instance Type** to the right side of **Type** is dimmed, it indicates that the instance is running. You must click **Stop** to stop the instance.

    ![Instance details](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/6221465261/p50527.png)

    In the Stop Instance dialog box, set **Stopped By** and **Stop Mode**, and then click **OK**.

7.  Click **Change Instance Type** to the right side of **Type**.

    ![Change instance type](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/3159987261/p292164.png)

8.  On the Change Instance Type page, select the instance type that you want to use and click **Change**.

    For more information about instance types, see [Select ECS instances to create worker nodes](/intl.en-US/Best Practices/Cluster/Select ECS instances to create the master and worker nodes of an ACK cluster.md).

9.  In the Change success dialog box, click **Console**.

    You are redirected to the Instances page.

    ![Instance list](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/1947465261/p50514.png)

    You can verify that the instance type of the worker-k8s-for-cs node is changed.

10. Find the worker-k8s-for-cs instance and choose **More** \> **Instance Status** \> **Start** in the **Actions** column. In the Start Instance dialog box, click **OK**. After the worker-k8s-for-cs node is added to the cluster, the node changes to the **Running** state. This indicates that the configurations of the work node are upgraded.


