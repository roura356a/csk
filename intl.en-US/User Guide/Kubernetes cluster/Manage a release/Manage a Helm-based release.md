# Manage a Helm-based release {#concept_tcp_sb4_vdb .concept}

The Alibaba Cloud Kubernetes service enriches the cloud marketplace, in which the app catalog and service catalog functions integrate with the Helm package management tool and allow you to build the application in the cloud quickly. A chart can be released multiple times, which requires you to manage the release versions. Therefore, the Alibaba Cloud Kubernetes service provides the release function, which allows you to manage the applications released by using Helm in the Container Service console.

## Prerequisites {#section_ldn_dc4_vdb .section}

-   You have created a Kubernetes cluster successfully. For how to create a Kubernetes cluster, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   You have used the app catalog function or service catalog function to install a Helm application. For more information, see [Simplify Kubernetes application deployment by using Helm](reseller.en-US/User Guide/Kubernetes cluster/Applications/Simplify Kubernetes application deployment by using Helm.md#). In this document, use the wordpress-default application as an example.

## View release details {#section_xsk_fc4_vdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Under Kubernetes, click **Application** \> **Release**in the left-side navigation pane. Select the cluster from the Clusters drop-down list.

    You can view the applications released by using the Helm package management tool and their services in the selected cluster.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6918/15407794134575_en-US.png)

3.  Take the wordpress-default as an example. Click **Detail** at the right of the release.

    You can view the release details such as the current version and history version of this release \(in this example, the current version is 1 and no history version exists\). You can also view the resource information of wordpress-default, such as the resource name and resource type, and view the YAML information.

    **Note:** Click the resource name and you are redirected to the Kubernetes dashboard to view the detailed running status of this resource.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6918/15407794134576_en-US.png)

4.  Click the **Values** tab to view the parameter configurations for installing the Helm package.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6918/15407794134577_en-US.png)


## Update a release version {#section_qmz_gc4_vdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Under Kubernetes, click **Application** \> **Release**in the left-side navigation pane. Select the cluster from the Clusters drop-down list.

    You can view the applications released by using the Helm package management tool and their services in the selected cluster.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6918/15407794134575_en-US.png)

3.  Take the wordpress-default as an example. Click **Update** at the right of the release to update the release. The Update Release dialog box appears.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6918/15407794134579_en-US.png)

4.  Modify the parameters and then click **Update**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6918/15407794144580_en-US.png)

    The current version is changed to 2 and you can find version 1 under History Version. To roll back the release, click **Rollback**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6918/15407794144582_en-US.png)


## Delete a release {#section_nsk_fc4_vdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Under Kubernetes, click**Application** \> **Release**in the left-side navigation pane. Select the cluster from the Clusters drop-down list.

    You can view the applications released by using the Helm package management tool and their services in the selected cluster.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6918/15407794134575_en-US.png)

3.  Take the wordpress-default as an example. Click **Delete** at the right of the release to delete the release. The Delete dialog box appears.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6918/15407794144583_en-US.png)

4.  Select the **Purge** check box to clear the release records if necessary. Click **OK** to delete the wordpress-default application and its resources such as the services and deployments.

