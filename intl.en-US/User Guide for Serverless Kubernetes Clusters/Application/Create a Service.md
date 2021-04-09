---
keyword: [serverless Kubernetes Service, ASK]
---

# Create a Service

This topic describes how to create a Service in a serverless Kubernetes \(ASK\) cluster.

A serverless Kubernetes \(ASK\) cluster is created. For more information, see [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md).

A Kubernetes Service, also known as a microservice, is an abstraction that defines a logical set of pods and a policy that can be used to access the pods. A label selector is used to determine which set of pods is targeted by a Service.

Each pod has a separate IP address. Pods are dynamically created and deleted. Therefore, using pods to provide external services is not a practical solution to guarantee high availability. Service abstraction decouples frontend clients from backend pods. The frontend clients do not need to be aware of which backend pods are used. This provides a loosely decoupled microservice architecture.

For more information, see [Kubernetes Services](https://kubernetes.io/docs/concepts/services-networking/service).

## Step 1: Create a Deployment

Create a Deployment from an image. In this example, a Deployment named serverless-app-deployment is created. For more information, see [Create an application from an image](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Application/Create an application from an image.md).

## Step 2: Create a Service

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Serverless Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column.

4.  In the left-side navigation pane of the details page, choose **Services and Ingresses** \> **Services**.

5.  In the upper-right corner of the Services page, click **Create**.

6.  In the Create Service dialog box, set the following parameters:

    -   **Name**: Enter a name for the Service. In this example, nginx-svc is used.
    -   **Type**: Select the type of Service. This parameter determines how the application is accessed.
        -   Cluster IP: exposes the Service through an internal IP address of the cluster. If you select this option, the Service is accessible only from within the cluster. This is the default value.

            **Note:** The **Headless Service** option is available only if you select **Cluster IP**.

        -   Server Load Balancer: exposes the Service by using a Server Load Balancer \(SLB\) instance. If you select this option, you can enable internal or external access to the Service. You can also use the SLB instance to route requests to NodePort and ClusterIP Services.

            **Note:** You can create a new SLB instance or use an existing SLB instance for a LoadBalancer Service. Multiple Services can share the same SLB instance. However, take note of the following limits:

            -   If you use an existing SLB instance, the listeners of the SLB instance overwrite those of the Service.
            -   If an SLB instance is created for a Service, you cannot reuse this SLB instance to expose other Services. Otherwise, the SLB instance may be accidentally deleted. Only SLB instances that are created in the console or by calling the API can be reused.
            -   Services that use the same SLB instance cannot use the same listener port. Otherwise, port conflicts may occur.
            -   When you reuse an SLB instance, the names of listeners and vServer groups are used as unique identifiers in Kubernetes. Do not modify the names of listeners or vServer groups.
            -   You cannot use an SLB instance to expose Services across clusters.
    -   **Backend**: Select the backend application for the Service. In this example, the previously created serverless-app-deployment application is selected. If you do not associate the Service with a backend application, no Endpoint object will be created. You can also manually bind the Service to an Endpoint object. For more information, see [Create a Service without selectors](https://kubernetes.io/docs/concepts/services-networking/service/#services-without-selectors).
    -   **External Traffic Policy**: Select Local or Cluster.

        **Note:** **External Traffic Policy** is available only if you select **Node Port** or **Server Load Balancer**.

    -   **Port Mapping**: Set the Service port and container port. The container port must be the same as the one that is exposed in the backend pod.
    -   **Annotations**: Add an annotation to the Service and configure parameters for the SLB instance. For example, the annotation `service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth:20` specifies that the Service bandwidth is 20 Mbit/s. This limits the traffic that flows through the Service. For more information about annotations, see [Use annotations to configure load balancing](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Use annotations to configure load balancing.md).
    -   **Label**: Add labels to the Service.
7.  Click **Create**. After the nginx-svc Service is created, it appears on the Services page.


