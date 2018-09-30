# Update a service {#concept_dk3_q2n_vdb .concept}

You can update a service in the Container Service console or Kubernetes dashboard.

## Update a service in Container Service console {#section_vr1_v2n_vdb .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).
2.  Under Kubernetes, click **Application \>** \> **Service **in the left-side navigation pane.
3.  Select the cluster and namespace from the Clusters and Namespace drop-down lists. Click **Update** at the right of the service \(nginx-svc in this example\).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6903/15382917614487_en-US.png)

4.  The Update dialog box appears. Modify the template. In this example, change the nodePort to **31000**. Then, click **OK**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6903/15382917614488_en-US.png)

5.  On the Service List page, view the changes of the service. In this example, the nodePort is changed as follows.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6903/15382917614489_en-US.png)


## Update a service in Kubernetes dashboard {#section_rzd_x2n_vdb .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).
2.  Under Kubernetes, click **Clusters** in the left-side navigation pane.
3.  Click **Dashboard** at the right of the cluster to go to the Kubernetes dashboard.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6903/15382917614490_en-US.png)

4.  In the Kubernetes dashboard, select the corresponding namespace and click **Services** in the left-side navigation pane.
5.  Click the icon at the right of the service and then select **View/edit YAML** from the drop-down list.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6903/15382917614491_en-US.png)

6.  The Edit a Service dialog box appears. Modify the configurations. In this example, change the nodePort to **31000**. Then, click **UPDATE**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6903/15382917614492_en-US.png)


