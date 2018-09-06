# Update a config map {#concept_swp_xpn_vdb .concept}

You can modify the configurations of a config map.

**Note:** Updating a config map affects applications that use this config map.

## Update a config map on Config Maps page {#section_l3m_zpn_vdb .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).
2.  Under Kubernetes, click **Application** \> **Config Maps** in the left-side navigation pane.
3.  Select the cluster and namespace from the Clusters and Namespace drop-down lists. Click **Modify** at the right of the config map.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15757/153622188510754_en-US.png)

4.  Click **Confirm** in the displayed dialog box.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15757/153622188510755_en-US.png)

5.  Modify the configurations.

    -   Click **Edit** on the right of the configuration you want to modify. Update the configuration and then click **Save**.
    -   You can also click **Edit YAML file**. Click **OK** after making the modifications.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15757/153622188510756_en-US.png)

6.  After modifying the configurations, click **OK**.

## Update a config map in Kubernetes dashboard {#section_ylt_1qn_vdb .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).
2.  Under Kubernetes, click **Clusters** in the left-side navigation pane.
3.  Click **Dashboard**at the right of the cluster.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15757/153622188510757_en-US.png)

4.  Under Kubernetes, select a namespace, click **Config and Storage** \> **Secrets** in the left-side navigation pane. Select the target secret and click**Actions** \> **View/edit YAML**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15757/153622188510758_en-US.png) 

5.  The Edit a Secret dialog box appears. Modify the configurations and then click **UPDATE**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15757/153622188510759_en-US.png)


