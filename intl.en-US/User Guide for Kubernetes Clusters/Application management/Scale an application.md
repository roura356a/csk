# Scale an application

This topic describes how to scale in or out an application as required after you create the application.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  Select the namespace where the service is deployed, find the deployment for the new version, and then click **Scale** in the Actions column.

    ![Deployments](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4983662061/p9942.png)

6.  In the dialog box that appears, set Desired Number of Pods to 4 and click **OK**.

    **Note:** By default, a deployment is updated based on the rollingUpdate strategy. This allows you to specify the minimum number of pods to run during the update. You can change this number in the template file.


