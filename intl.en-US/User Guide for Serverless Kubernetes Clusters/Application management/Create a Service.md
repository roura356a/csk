---
keyword: [serverless Kubernetes Services, ASK]
---

# Create a Service

This topic describes how to create a Service in a serverless Kubernetes \(ASK\) cluster.

A serverless Kubernetes \(ASK\) cluster is created. For more information, see [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md).

A Kubernetes Service, also known as a microservice, is an abstraction that defines a logical set of pods and a policy that can be used to access the pods. A label selector is used to determine which set of pods is targeted by a Service.

Each pod has a separate IP address. Pods are dynamically created and deleted. Therefore, using pods to provide external services is not a practical solution to guarantee high availability. Service abstraction decouples frontend clients and backend pods. The frontend clients do not need to be aware of which backend pods are used. This provides a loosely decoupled microservice architecture.

For more information, see [Kubernetes Services](https://kubernetes.io/docs/concepts/services-networking/service).

## Step 1: Create a Deployment

Create a Deployment from an image. In this example, a Deployment named serverless-app-deployment is created. For more information, see [Create an application from an image](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Application management/Create an application from an image.md).

## Step 2: Create a Service

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Serverless Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column.

4.  In the left-side navigation pane, click **Services**.

5.  In the upper-right corner of the page, click **Create**.

6.  In the Create Service dialog box, set the following parameters:

    -   **Name**: the name of the Service. In this example, the Service name is nginx-svc.
    -   **Type**: the type of the Service. This parameter specifies how the Service is accessed.
        -   Cluster IP: exposes the Service through an internal IP address in the cluster. This type of Service is equivalent to a ClusterIP type Service. If you select Cluster IP, the Service is accessible from only within the cluster. This is the default setting.

            **Note:** The **Headless Service** option is available only when you select **Cluster IP**.

        -   Server Load Balancer: exposes the Service through an Server Load Balancer \(SLB\) instance. This type of Service is equivalent to a LoadBalancer type Service. If you select Server Load Balancer, you can enable internal access or external access for the Service. An SLB instance can route access to NodePort and ClusterIP type Services.

            **Note:** You can create a new SLB instance or use an existing SLB instance for a LoadBalancer type Service. Multiple Services can share the same SLB instance. However, you must note the following limits:

            -   If you use an existing SLB instance, the listeners of the SLB instance will be overwritten.
            -   If an SLB instance is created when you create a Service, you cannot reuse this SLB instance when you create other Services. Otherwise, the SLB instance may be deleted. You can reuse only SLB instances that are manually created in the console or by calling the API.
            -   Services that reuse the same SLB instance cannot use the same listener port. Otherwise, port conflicts may occur.
            -   When you reuse an SLB instance, the names of listeners and VServer groups are deemed as the unique identifiers of these resources in Kubernetes. Do not modify the names of these resources.
            -   You cannot share SLB instances across clusters.
    -   **Backend**: the backend object that you want to associate with the Service. In this example, select nginx-deployment-basic that is created in preceding steps. If you do not associate the Service with a backend object, no Endpoint object will be created. You can also manually bind the Service to an Endpoint object. For more information, see [Create a Service without selectors](https://kubernetes.io/docs/concepts/services-networking/service/#services-without-selectors).
    -   **External Traffic Policy**: Select Local or Cluster.

        **Note:** **External Traffic Policy** is available only when you select **Node Port** or **Server Load Balancer**.

    -   **Port Mapping**: Set the service port and container port. The container port must be the same as the one that is exposed by the backend pod.
    -   **Annotations**: Add annotations to the Service and configure parameters for the SLB instance. For example, an annotation `service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth:20` specifies that the Service bandwidth is set to 20 Mbit/s. This allows you to regulate data transfer to the Service. For more information about annotations, see [Use annotations to configure SLB instances](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Use annotations to configure SLB instances.md).
    -   **Label**: Add labels to the Service.
7.  Click **Create**. After the nginx-svc Service is created, it appears on the Services page.

8.  To visit the nginx-svc welcome page, you can use your browser to access the external endpoint of the Service.


