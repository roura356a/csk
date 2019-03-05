# Update a service {#concept_dk3_q2n_vdb .concept}

This topic describes how to update a Kubernetes service in the Container Service console or the Kubernetes dashboard.

## Update a service in the Container Service console {#section_vr1_v2n_vdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Kubernetes, choose **Discovery and Load Balancing** \> **Service**.
3.  Select the target cluster and namespace, and then click **Update** on the right of the target service. In this example, the target service is named nginx-svc.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16666/155176744711039_en-US.png)

4.  In the displayed dialog box, update the service parameters. Then, click **Update**. In this example, the service tag is updated from app:nginx-v1 to app:nginx-v2.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16666/155176744711041_en-US.png)

5.  In the service list, click **Details** on the right of the target service to view the service updates. In this example, the updated service tag app:nginx-v2 is displayed.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16666/155176744711042_en-US.png)


## Update a service in the Kubernetes dashboard {#section_rzd_x2n_vdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Kubernetes, click **Clusters**.
3.  Click **Dashboard** on the right of the target cluster.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16666/155176744711043_en-US.png)

4.  In the Kubernetes dashboard, select the target namespace and then click **Services** in the left-side navigation pane.
5.  Click the icon on the right of the target service and then click **View/edit YAML**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16666/155176744711044_en-US.png)

6.  In the displayed dialog box, modify the service settings. Then, click **UPDATE**. In this example, the nodePort is changed to **31000**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16666/155176744711045_en-US.png)


