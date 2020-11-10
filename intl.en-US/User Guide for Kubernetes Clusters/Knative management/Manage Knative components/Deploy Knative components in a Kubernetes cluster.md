---
keyword: [Deploy Knative, Kubernetes]
---

# Deploy Knative components in a Kubernetes cluster

Knative is a Kubernetes-based serverless framework. The main objective of Knative is to establish a cloud-native and cross-platform orchestration standard for serverless applications. This topic describes how to deploy Knative components in a cluster of Container Service for Kubernetes \(ACK\).

-   [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md).

    **Note:**

    -   Knative 0.14.0 and later versions support only clusters of Kubernetes 1.15 and later. ACK Standard clusters, serverless Kubernetes \(ASK\) clusters, and clusters of ACK Proprietary Edition are supported.
    -   For ACK Standard clusters and clusters of ACK Proprietary Edition, they must contain at least three worker nodes.
-   [Deploy Istio](/intl.en-US/User Guide for Kubernetes Clusters/Service mesh/Istio management/Deploy Istio.md).

    To enable Tracing Analysis, you must set Istio configurations. For more information, see [Enable Tracing Analysis on Knative](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Best practices/Enable Tracing Analysis on Knative.md).


1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Knative** \> **Services**.

3.  In the upper-right corner of the Knative Components page, click **Deploy**.

4.  Select the Knative components that you want to install and click **Deploy**.

    -   **Tekton**: provides a flexible approach to create images from source code.
    -   You can use **Knative Serving** to manage serverless applications. Knative Serving enables automatic scaling for pods where serverless applications are deployed based on Knative events and user requests. If no workload is processed, the number of pods is scaled to zero.
    -   **Knative Eventing**: provides event management capabilities, such as producing and consuming events.

After the deployment is complete, you can perform the following steps to view the result:

-   Click **Go to Components** to view component information.
-   Click **Go to Applications** to view application-related operations.

![Verify the deployment result](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8955359951/p48854.png)

