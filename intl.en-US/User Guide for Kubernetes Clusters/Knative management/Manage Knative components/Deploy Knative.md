---
keyword: [Deploy Knative, Kubernetes]
---

# Deploy Knative

Knative is a Kubernetes-based serverless framework. The main objective of Knative is to develop a cloud-native and cross-platform orchestration standard for serverless applications. This topic describes how to deploy Knative in a Container Service for Kubernetes \(ACK\) cluster.

-   [Create a managed kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md).

    **Note:**

    -   Knative 0.14.0 and later versions support only clusters of Kubernetes 1.15 and later. The following types of ACK clusters are supported: standard managed Kubernetes clusters, standard dedicated Kubernetes clusters, and serverless Kubernetes \(ASK\) clusters.
    -   Standard managed Kubernetes clusters and standard dedicated Kubernetes clusters must contain at least three worker nodes.
-   [Deploy Istio](/intl.en-US/User Guide for Kubernetes Clusters/Service mesh/Istio management/Deploy Istio.md).

    To enable Tracing Analysis, you must set Istio configurations. For more information, see [Activate Tracing Analysis on Knative](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Best practices/Activate Tracing Analysis on Knative.md).


1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane, click **Knative** \> **Components**.

4.  On the Knative Components page, click **Deploy Knative** in the upper-right corner of the page.

5.  After you select the Knative components that you want to install, click **Deploy**.

    -   **Tekton**: provides a flexible approach to create images from source code.
    -   **Knative Serving**: manages serverless applications. Knative Serving enables automatic scaling for pods where serverless applications are deployed based on Knative events and user requests. If no workload is processed, the number of pods is scaled to zero.
    -   **Knative Eventing**: provides event management capabilities, such as producing and consuming events.

After Knative is deployed, you can perform the following steps to view the result:

-   Click **Go to Components** to view component information.
-   Click **Go to Applications** to view Knative application-related operations.

![Check the deployment result](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8955359951/p48854.png)

