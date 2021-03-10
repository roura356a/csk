---
keyword: [batch management, OOS, ACK node]
---

# Manage nodes in batches

You can use Operation Orchestration Service \(OOS\) to manage cluster nodes in batches. This allows you to enhance operations efficiency. For example, you can obtain logs of multiple nodes at the same time. This topic describes how to manage nodes in batches.

-   A Kubernetes cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   OOS is activated in the [OOS console](https://partners-intl.aliyun.com/login-required#/oos).

## Procedure

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  In the left-side navigation pane, choose **Clusters** \> **Nodes**.

4.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

5.  Select the cluster and the nodes that you want to manage and click **Set to Unschedulable**.

6.  In the Batch Operations dialog box, select the operations and maintenance \(O&M\) task that you want to run and click **OK**.

7.  Set the parameters to run OOS tasks.

    For more information about the parameters, see .

8.  After you set the parameters, click **Create**.

    You are redirected to the Executions page in the [OOS console](https://partners-intl.aliyun.com/login-required#/oos) by default. You can find the newly submitted execution.

    -   If you set Execution Mode to **Automatic**, the execution automatically starts. After the execution is complete, check the execution status.
    -   If you set Execution Mode to **Manual**, you need to manually start the execution.
    To view the execution status, click the ID of the execution that you want to view or click **Details** in the **Actions** column.


