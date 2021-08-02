---
keyword: [one-click deployment, WordPress]
---

# Deploy WordPress from App Catalog

This topic describes how to deploy a WordPress application in a Container Service for Kubernetes \(ACK\) cluster by using App Catalog.

An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

**Note:** You can deploy a WordPress application by using App Catalog only in ACK clusters. Serverless Kubernetes \(ASK\) clusters are not supported.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**.

3.  On the App Catalog page, click the **App Hub** tab. Find **wordpress** and click **wordpress 5.3.0**.

    ![wordpress](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/7339987261/p142677.png)

4.  On the wordpress page, set **Cluster**, **Namespace**, and **Release Name** in the **Deploy** section.

5.  Click the **Parameters** tab, set persistence to false, and then click **Create**.

    -   Set persistence to false, as shown in the following figure.

        ![2](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9055674161/p142060.png)

    -   Set persistence to false, as shown in the following figure.

        ![1](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9055674161/p142059.png)

6.  In the left-side navigation pane of the ACK console, click **Clusters**.

7.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

8.  In the left-side navigation pane of the details page, choose **Network** \> **Services**.

9.  On the Services page, find the Service that is created for the WordPress application and click the hyperlink in the External Endpoint column. The WordPress homepage appears.

    ![Homepage](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/0058196261/p142679.png)

    **Note:** To modify the parameters after the WordPress application is deployed, delete and redeploy the application. In the left-side navigation pane of the cluster details page, choose **Applications** \> **Helm**. On the Helm tab, find and delete the WordPress release. Then, perform the preceding steps to redeploy the WordPress application.


