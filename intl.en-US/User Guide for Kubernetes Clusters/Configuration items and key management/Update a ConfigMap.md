# Update a ConfigMap

This topic describes how to update a ConfigMap in the Container Service for Kubernetes \(ACK\) console.

If you update a ConfigMap, the applications that use this ConfigMap will be affected.

## Update a ConfigMap on the ConfigMap tab

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Configurations** \> **ConfigMaps**.

5.  On the **ConfigMap** tab, find the ConfigMap that you want to update and click **Edit** in the Actions column.

6.  In the **Edit** panel, modify the keys and values based on your requirements and click **OK**.

    **Note:** You can also modify the keys and values in the YAML file. On the **ConfigMap** tab, click **YAML** in the Actions column. In the **View in YAML** panel, modify the keys and values based on your requirements and click **OK**.


## Update a ConfigMap on the Kubernetes dashboard

1.  In the left-side navigation pane of the ACK console, click **Clusters**.

2.  On the Clusters page, find the cluster where the ConfigMap that you want to update is stored and choose **More** \> **Dashboard** in the **Actions** column.

3.  In the left-side navigation pane of the Kubernetes dashboard, choose **Config and Storage** \> **Config Maps**. On the Config Maps page, find the ConfigMap and choose **![Actions](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2249384161/p160872.png)** \> **View/edit YAML** on the right side of the page.

4.  In the **Edit a Config Map** dialog box, modify the ConfigMap based on your requirements and click **UPDATE**.

    ![Update a ConfigMap](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9031674161/p10759.png)


