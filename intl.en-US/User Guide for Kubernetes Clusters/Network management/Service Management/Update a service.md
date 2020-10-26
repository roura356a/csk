# Update a service

This topic describes how to update a Kubernetes service in the Container Service for Kubernetes \(ACK\) console or by using the Kubernetes dashboard.

## Update a service in the ACK console

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Services**.

5.  Specify a cluster and a namespace, find the service that you want to update, and then click **Update** in the Actions column of the service. In this example, the nginx-svc service is updated.

6.  In the dialog box that appears, modify the configurations based on your business requirements and click **Update**.

    ![Modify the service](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8345359951/p11041.png)

7.  Find the service on the Services page and click **Details** in the Actions column to view configuration changes. In this example, the label of the service is updated.

    ![View the updated result](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8345359951/p11042.png)


## Update a service by using the Kubernetes dashboard

1.  In the left-side navigation pane, click **Clusters**.

2.  On the Clusters page, find the cluster that you want to manage and choose **More** \> **Dashboard** in the **Actions** column of the cluster.

3.  On the Kubernetes dashboard, specify a namespace and click **Services** in the left-side navigation pane.

4.  Find the service that you want to view, click the icon ![image1](../images/p148254.png) on the right of the service, and then select **View/edit YAML**.

    ![Update a service](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8345359951/p11044.png)

5.  In the dialog box that appears, modify the configurations. For example, change the value of nodePort to 31000. Then, click **UPDATE**.


