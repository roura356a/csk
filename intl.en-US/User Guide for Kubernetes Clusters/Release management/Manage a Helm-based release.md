# Manage a Helm-based release

Container Service for Kubernetes \(ACK\) integrates the package manager Helm to help you deploy applications to the cloud in an efficient manner. You can install a single chart multiple times into the same cluster. Each time you install a chart, a new release is created. This causes difficulties in release management. To address this issue, ACK enables the release feature that allows you to manage Helm-based application releases in the ACK console.

-   A cluster is created. For more information, see [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md).
-   A Helm-based application is deployed to the cluster. You can deploy an application through the App Catalog module or the Service Catalog module in the ACK console. For more information, see [Simplify application deployment by using Helm](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Simplify Kubernetes application deployment by using Helm.md). The following examples are based on an application named tf-model.

## View release details

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Releases**.

5.  Click the **Helm** tab. The list of releases appears.

6.  View release details. For example, you can find the tf-model release and click **Details** in the Actions column. The details page of tf-model appears.

    You can view information about tf-model, including the current version, history versions, resource names, resource types, and YAML files. In this example, tf-model does not have history versions and the current version is 1.

    **Note:** To view the detailed running status of a resource, click the name of the resource. The dashboard page appears.

    ![The details page of tf-model](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2455359951/p14017.png)

7.  Click the **Parameters** tab. You can view the parameters of tf-model.

    ![Configure parameters](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2455359951/p14018.png)


## Update a release

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Releases**.

5.  Click the **Helm** tab. The list of releases appears.

6.  Update a release. For example, you can find the tf-model release and click **Update** in the Actions column. The Update Release dialog box appears.

    ![Update a release](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2455359951/p14019.png)

7.  In the Update Release dialog box, modify the parameter values based on your needs and click **Update**.

    ![Modify parameter values](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2455359951/p14020.png)

    On the details page of tf-model, the current version of tf-model changes to 2. In the History Version section, you can find the history version 1. To roll back to version 1, click **Roll Back**.

    ![Rollback](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2455359951/p14021.png)


## Delete a release

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Releases**.

5.  Click the **Helm** tab. The list of releases appears.

6.  Delete a release. For example, you can find the tf-model release and click **Delete** in the Actions column.

7.  In the Delete dialog box, select the **Clear Release Records** check box and click **OK**. The tf-model release is deleted. After you delete the release, the resources of the release are deleted, including the services and deployments.

    ![Delete a release](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3363662061/p148067.png)

    **Note:** If you clear the **Clear Release Records** check box, the deletion of tf-model does not take effect. You can find tf-model in the release list. If you create another release named tf-model, a release name conflict occurs.


