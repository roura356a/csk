# Deploy WordPress from App Catalog

This topic describes how to deploy the WordPress application in a cluster of Container Service for Kubernetes \(ACK\) from App Catalog.

An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

**Note:** You can deploy the WordPress application in only ACK clusters. Serverless Kubernetes \(ASK\) clusters are not supported.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**.

3.  On the App Catalog page, click the **App Hub** tab. In the upper-right corner of the App Catalog page, enter wordpress into the Name search box and click the search icon. Click wordpress 5.3.0.

4.  On the App Catalog - wordpress page, click the **Parameters** tab. Set persistence to false.

    -   Set persistence to false, as shown in the following figure.

        ![2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9055674161/p142060.png)

    -   Set persistence to false, as shown in the following figure.

        ![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9055674161/p142059.png)

5.  In the Deploy section, set **Cluster**, **Namespace**, and **Release Name**, and click **Create**.

6.  In the left-side navigation pane of the ACK console, click **Clusters**.

7.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Details** in the **Actions** column. The details page of the cluster appears.

8.  In the left-side navigation pane of the details page, choose **Services and Ingresses** \> **Services**.

9.  On the Services page, find the Service that is related to the WordPress application and click the hyperlink in the External Endpoint column. The WordPress homepage appears.

    **Note:** If you want to modify the parameters after the WordPress application is deployed, delete and redeploy the application. In the left-side navigation pane of the cluster details page, click **Releases**. On the Releases page, click the Helm tab. On the Helm tab, find and delete the Helm chart of the WordPress application. Then, perform the preceding steps to redeploy the WordPress application.

    ![Homepage](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9055674161/p142679.png)


