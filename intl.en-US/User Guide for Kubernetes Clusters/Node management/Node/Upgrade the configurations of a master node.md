---
keyword: [master nodes, modify configurations, upgrade configurations of master nodes]
---

# Upgrade the configurations of a master node

This topic describes how to upgrade the configurations of a master node. Master nodes in a dedicated Kubernetes cluster are managed by users. You may need to upgrade the configurations of master nodes in a cluster when the size of the cluster grows.

[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  Find the three master nodes that you want to manage and click the instance ID of a master node. In this example, master-01 is selected. On the Instance Details page,

    you can view instance information such as the instance type.

    ![Instance Details page](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1246858951/p50527.png)

5.  In the Configuration Information section, click **Change Instance Type**.

    ![Change instance type](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1246858951/p50517.png)

    If the **Change Instance Type** button is dimmed, it indicates that the instance is running. Before you change the instance type, you must click **Stop** in the upper-right corner to stop the instance.

6.  On the Instance Type page, select an instance type and click **Confirm**. For more information, see [Select ECS instances to create the master nodes](/intl.en-US/Best Practices/Cluster/Select ECS instances to create the master and worker nodes of an ACK cluster.md).

7.  In the Change success message, click **OK**.

    Then, you are redirected to the Instances page.

    ![Instances page](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1246858951/p50514.png)

    The instance type of master-01 is changed.

8.  Select the instance and click **Start**. Then, master-01 is automatically added to the cluster and the state of the node changes to **Running**, as shown in the following figure.

    ![Nodes](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2246858951/p50530.png)

9.  Repeat Step 4 to Step 7 to upgrade configurations of the other master nodes.

    **Note:** The master nodes in this topic are billed on a pay-as-you-go basis. For more information about how to upgrade master nodes that are charged by other billing methods, see [Overview of instance upgrade and downgrade](/intl.en-US/Instance/Change configurations/Overview of instance upgrade and downgrade.md).


