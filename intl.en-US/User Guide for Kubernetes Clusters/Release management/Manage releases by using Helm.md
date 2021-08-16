---
keyword: [Helm, release management]
---

# Manage releases by using Helm

Container Service for Kubernetes \(ACK\) integrates the package manager Helm to help you deploy applications to the cloud in an efficient manner. You can install a chart multiple times in a Kubernetes cluster. Each time you install a chart, a release is created. This requires you to manage release versions. To meet this requirement, ACK provides the release feature that allows you to manage applications deployed by using Helm in the ACK console.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   An application is installed by using Helm on the App Catalog page. For more information, see [Use Helm to simplify application deployment](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Application Scheduling Deployment/Use Helm to simplify application deployment.md). In this example, an application named tf-model is deployed.

## View release details

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Applications** \> **Helm**.

5.  You can view the details of a release. In this example, find tf-model and click **View Details** in the Actions column. The details page of tf-model appears.

    You can view information about tf-model, such as the current version and history versions. In this example, the current version is 1 and no history version exists. You can also view information about the resources of tf-model, such as the names, types, and YAML files.

    **Note:** To view the status of a resource, click the name of the resource to go to the Kubernetes dashboard page.

    ![The details page of tf-model](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/2455359951/p14017.png)

6.  Click the **Parameters** tab. You can view the parameters of the Helm chart.

    ![Set parameters](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/2455359951/p14018.png)


## Update a release

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Applications** \> **Helm**.

5.  You can update a release. In this example, find tf-model and click **Update** in the Actions column. The Update Release dialog box appears.

    ![Update a release](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5545072261/p14019.png)

6.  In the Update Release dialog box, modify the parameters based on your requirements and click **Update**.

    ![Modify parameters](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/2455359951/p14020.png)

    On the details page of tf-model, the current version of tf-model changes to 2. In the History Version section, you can find the history version 1. To roll back to version 1, click **Roll Back**.

    ![Roll back a release](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/2455359951/p14021.png)


## Delete a release

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Applications** \> **Helm**.

5.  You can delete a release. In this example, find the tf-model release and click **Delete** in the Actions column.

6.  In the Delete dialog box, select **Clear Release Records** and click **OK**. After the release is deleted, the resources of the release are deleted, including the Services and Deployments.

    ![Delete a release](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/3363662061/p148067.png)

    **Note:** If you clear **Clear Release Records**, the deletion of tf-model does not take effect. You can still find tf-model in the release list. If you create another release named tf-model, a release name conflict occurs.


