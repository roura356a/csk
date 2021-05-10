---
keyword: [deploy Knative, Kubernetes]
---

# Deploy Knative

Knative is a Kubernetes-based serverless framework. The main objective of Knative is to develop a cloud-native and cross-platform orchestration standard for serverless applications. This topic describes how to deploy Knative in a Container Service for Kubernetes \(ACK\) cluster.

[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

**Note:**

-   Knative 0.14.0 and later support only clusters of Kubernetes 1.15 and later. The following types of ACK clusters are supported: standard managed Kubernetes clusters, standard dedicated Kubernetes clusters, and serverless Kubernetes \(ASK\) clusters.
-   If a standard managed Kubernetes cluster or a standard dedicated Kubernetes cluster is used, the cluster must contain at least three worker nodes.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, choose **Applications** \> **Knative**.

5.  On the Components tab, click **Deploy Knative**.

6.  Select the Knative components that you want to install and click **Deploy**.

    -   **Serving**: manages serverless applications. Knative Serving enables automatic scaling for pods where serverless applications are deployed based on Knative events and user requests. If no workload is processed, the number of pods is scaled to zero.
    -   **Eventing**: provides event management capabilities, such as producing and consuming events.
    -   **Tekton**: provides a flexible approach to create images from source code.

After Knative is deployed, you can perform the following steps to check the deployment result:

-   Click **Go to Components** to view information about the components.
-   Click **Go to Applications** to view information about operations related to Knative applications.

![Check the deployment result](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8955359951/p48854.png)

