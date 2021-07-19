---
keyword: delete an ASK cluster
---

# Delete an ASK cluster

This topic describes how to delete a serverless Kubernetes \(ASK\) cluster in the Container Service for Kubernetes \(ACK\) console.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to delete and choose **More** \> **Delete** in the **Actions** column.

4.  If the deletion protection feature is enabled for the cluster, click the name of the cluster or click **Details** in the **Actions** column.

    ![25](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3115666261/p272858.png)

    1.  On the Cluster Information page, click the **Basic Information** tab.

        ![26](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3115666261/p272851.png)

    2.  Turn off **Deletion Protection**.

    3.  Return to the **Clusters** page.

    4.  On the Clusters page, find the cluster that you want to delete and choose **More** \> **Delete** in the **Actions** column.

5.  In the Delete Cluster dialog box, read and select **I understand the above information and want to delete the specified cluster**, and then click **OK**.

    **Note:** If you have created elastic container instances to run pods in the cluster, you must go to the **Deployments** page and delete all pods that run on the elastic container instances. Then, perform the preceding steps to delete the ASK cluster.


