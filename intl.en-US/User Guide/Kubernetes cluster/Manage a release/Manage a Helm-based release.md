# Manage a Helm-based release {#concept_tcp_sb4_vdb .concept}

Alibaba Cloud Container Service for Kubernetes is integrated with the package management tool Helm to help you quickly deploy applications on the cloud. However, Helm charts can be released multiple times and the release version must be managed. Container Service for Kubernetes provides a release function, which allows you to manage the applications released by using Helm in the Container Service console.

## Prerequisites {#section_ldn_dc4_vdb .section}

-   You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   You have installed a Helm application by using the App Catalog function or Service Catalog function. For more information, see [Simplify Kubernetes application deployment by using Helm](reseller.en-US/User Guide/Kubernetes cluster/Applications/Simplify Kubernetes application deployment by using Helm.md#). In this topic, the wordpress-default application is used as an example.

## View release details {#section_xsk_fc4_vdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane, select Container Service - Kubernetes. Then, select **Application** \> **Release** and click the Helm tab. Select the target cluster from the Clusters drop-down list.

    In the displayed release list, you can view the applications and services released through Helm in the selected cluster.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15770/154452145414016_en-US.png)

3.  Find your target release \(wordpress-default in this example\) and click **Details** to view the release details.

    You can view such release details as the current version and history version. In this example, the current version is 1 and no history version exists. On the Resource tab page, you can view the resource information of wordpress-default, such as the resource name and the resource type, and view the YAML information.

    **Note:** You can view the running status of the resource in details by clicking the resource name and going to the Kubernetes dashboard page.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15770/154452145414017_en-US.png)

4.  Click the **Values** tab to view the release parameters.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15770/154452145414018_en-US.png)


## Update a release version {#section_qmz_gc4_vdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane, select Container Service - Kubernetes. Then, select **Application** \> **Release** and click the Helm tab. Select the target cluster from the Clusters drop-down list.

    In the displayed release list, you can view the applications and services released through Helm in the selected cluster.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15770/154452145414016_en-US.png)

3.  Find your target release \(wordpress-default in this example\). Click **Update** and the Update Release dialog box appears.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15770/154452145414019_en-US.png)

4.  Modify the parameters and then click **Update**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15770/154452145414020_en-US.png)

    On the release list page, you can see that the current version changes to 2. To roll back to version 1, click Details and in the History Version area, click **Rollback**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15770/154452145414021_en-US.png)


## Delete a release {#section_nsk_fc4_vdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane, select Container Service - Kubernetes. Then, select **Application** \> **Release** and click the Helm tab. Select the target cluster from the Clusters drop-down list.

    In the displayed release list, you can view the applications and services released through Helm in the selected cluster.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15770/154452145414016_en-US.png)

3.  Find your target release \(wordpress-default in this example\). Click **Delete** and the Delete dialog box appears.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15770/154452145414022_en-US.png)

4.  Select the **Purge** check box if you want to clear the release records, and then click **OK**. After you delete a release, the related resources such as the services and deployments are deleted too.

