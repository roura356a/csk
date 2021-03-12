# Use an application trigger to redeploy an application

Container Service Kubernetes \(ACK\) supports application triggers. You can use application triggers for different methods. This topic describes how to use an application trigger to redeploy an application.

-   An ACK cluster is created. For more information, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).
-   An application is created. You can use this application to create and test a trigger. In this example, an NGINX application is created.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  On the Deployments tab, set the namespace, find the application that you want to manage, and then click **Details** in the **Actions** column of the application.

6.  On the NGINX application details page, click the **Triggers** tab and click **Create Trigger**.

7.  In the Create Trigger dialog box, set **Action** to **Redeploy** and click **OK**.

    **Note:** Only Redeploy is available in the **Action** drop-down list.

    ![Redeploy](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7145359951/p9899.png)

    After the trigger is created, a trigger link appears in the Trigger Link Address column on the Triggers tab.

    ![Trigger](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7145359951/p9900.png)

8.  Copy the link and open it in your browser. A message appears to display specific information such as the request ID.

    ![Trigger](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7145359951/p9901.png)

9.  Go to the NGINX application details page. A new pod appears on the page.

    ![New pod](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7145359951/p9904.png)

    After the new pod is deployed, the original pod is automatically deleted.


You can call a trigger by using GET or POST in a third-party system. For example, you can run the curl command to call a trigger.

To call the trigger to redeploy an application, run the following command:

`curl https://cs.console.aliyun.com/hook/trigger?token=xxxxxxx`

