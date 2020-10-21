# Overview

The Knative community version is a cloud-native and cross-platform orchestration engine for serverless applications. You can deploy Knative in a serverless Kubernetes \(ASK\) cluster only if you pay for the Knative controller. ASK Knative integrates Knative with ASK. To use cloud resources by calling Knative API operations, you only need to create an ASK cluster and enable the Knative feature. In this case, you do not need to pay for the Knative controller.

## Benefits of ASK Knative

|Knative community version|ASK Knative|
|-------------------------|-----------|
|By default, Istio gateways are used. Therefore, you must pay for some infrastructures that are used to install Istio controllers.|You do not have to pay for the Knative controller.|
|You must pay for some infrastructures that are used to install the Knative controller.|
|A cold start occurs when you create a pod in an ASK cluster. The scale to zero mechanism of the Knative community version reduces resource costs. However, during the cold start, the cluster may fail to process requests due to session timeout.|ASK Knative does not scale pods to zero during off-peak hours. The reserved instances can be used to avoid cold starts at low costs. For more information about reserved instances, see [t1956246.md\#]().|

## Resource management in Knative

ASK provides an simple way to use Kubernetes. To deploy containerized applications in ASK clusters, you do not have to purchase nodes. The following list describes the benefits of using Knative to manage resources:

-   You can use Knative to manage applications in ASK clusters.
-   Knative automatically requests pod resources from ASK clusters.

The Knative Serving controller is integrated with ASK. To use cloud resources by calling Knative API operations, you only need to create an ASK cluster and enable the Knative feature. You do not have to pay for the Knative controller.

## Knative Gateway

By default, the Knative community version supports multiple gateway solutions, such as Istio, Gloo, Contour, Kourier, and Ambassador. Among these solutions, Istio is most frequently used. This is because Istio also functions as a service mesh. Each ASK cluster must contain at least two resident gateway instances. The two instances provide backup for each other to ensure high availability. The gateway controllers must be resident. You must pay for the infrastructures and O&M of resident resources.

To improve user experience, Alibaba Cloud provides Knative Gateway based on Server Load Balancer \(SLB\). This feature provides gateway capabilities required by ASK clusters and is as stable and reliable as cloud services. It does not require resident resources and saves infrastructure and O&M costs.

## Reserved instances

By default, the Knative community version scales pods to zero during off-peak hours. This leads to a cold start the next time when the system starts the application. During a cold start, the system must allocate infrastructures, schedule pods, and pull the application image. A cold start takes longer than a hot start. The image size and the time required to start an application vary based on the application developer and the service.

ASK Knative does not scale pods to zero during off-peak hours. Instead, it reserves one pod. The following content describes how reserved instances work:

-   ASK Knative uses burstable instances to replace standard compute optimized instances during off-peak hours, and switches back to standard computing instances when requests arrive. This mechanism saves your costs during off-peak hours.
-   CPU credits that are accumulated during off-peak hours can be used during peak hours to reduce costs.

For more information about reserved instances, see [t1956246.md\#]().

