---
keyword: [Deploy Knative, Kubernetes]
---

# Deploy Knative

Knative is a Kubernetes-based serverless framework that is intended for developing a cloud-native and cross-platform orchestration standard for serverless applications. This topic describes how to deploy Knative in a Container Service for Kubernetes \(ACK\) cluster.

[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

**Note:**

-   Knative 0.14.0 and later versions support only clusters of Kubernetes 1.15 and later. The following types of ACK cluster are supported: standard managed Kubernetes clusters, standard dedicated Kubernetes clusters, and serverless Kubernetes \(ASK\) clusters.
-   Standard managed Kubernetes clusters and standard dedicated Kubernetes clusters must contain at least three worker nodes.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane, choose **Applications** \> **Knative**.

4.  On the Components tab, click **Deploy Knative**.

5.  In the **Core Components** section, select the components that you want to install and click **Deploy** in the **Actions** column.

    -   **Tekton**: provides a flexible approach for creating images from source code.
    -   **Serving**: manages serverless applications. Knative Serving enables automatic scaling for pods where serverless applications are deployed based on Knative events and user requests. If no workload is processed, the number of pods is scaled to zero.
    -   **Eventing**: provides event management capabilities, such as producing and consuming events.

After Knative is deployed, you can perform the following steps to view the result:

-   Click **Go to Components** to view component information.
-   Click **Go to Applications** to view Knative application-related operations.

![Check the result](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8955359951/p48854.png)

