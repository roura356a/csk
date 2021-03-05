# Overview

Open source Knative is a cloud-native and cross-platform orchestration engine for serverless applications. You can deploy open source Knative in serverless Kubernetes \(ASK\) clusters. However, you must pay additional fees. ASK is integrated with Knative. To use cloud resources by calling the Knative API, you only need to create an ASK cluster and enable Knative for the cluster. In this case, you do not need to pay for the Knative controller.

## Benefits of ASK Knative

|Open source Knative|ASK Knative|
|-------------------|-----------|
|By default, Istio gateways are used. Therefore, you must pay for the infrastructure resources that are used to install Istio controllers.|You do not need to pay for the Knative controller.|
|You are charged for the infrastructure resources that are used to install the Knative controller.|
|A cold start occurs when you create a pod in an ASK cluster. Open source Knative uses the scale-to-zero mechanism to reduce costs. However, during the cold start, the cluster may fail to process requests due to session timeout.|ASK Knative does not scale the number of instances to zero during off-peak hours. Instead, ASK uses reserved instances. Reserved instances can be used to avoid cold starts at low costs. For more information about reserved instances, see [Reserved instances](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Knative management/Service management/Reserved instances.md).|

## Resource management in Knative

ASK hosts serverless applications and provides an easy way to use Kubernetes. You can directly deploy containerized applications in ASK clusters without the need to purchase nodes. The following list describes the benefits of using Knative to manage resources:

-   You can use Knative to manage applications in ASK clusters.
-   Knative automatically requests pod resources from ASK clusters.

The Knative Serving controller is integrated with ASK. To use cloud resources by calling Knative API operations, you only need to create an ASK cluster and enable Knative for the cluster. You are not charged for the Knative controller.

## Knative Gateway

By default, open source Knative supports multiple gateway solutions, such as Istio, Gloo, Contour, Kourier, and Ambassador. Among these solutions, Istio is most frequently used. This is because Istio also functions as a service mesh. Each ASK cluster must contain at least two resident gateway instances. The two instances provide backup for each other to ensure high availability. The gateway controllers must be resident. You must pay infrastructure and O&M fees for these resident resources.

To improve user experience, Alibaba Cloud provides Knative Gateway based on Server Load Balancer \(SLB\) instances. Knative Gateway provides gateway capabilities required by ASK clusters and is as stable and reliable as cloud services. Resident resources are not required. This reduces infrastructure costs and O&M workloads.

## Reserved instances

By default, open source Knative scales the number of instances to zero during off-peak hours. This leads to a cold start the next time when the system starts the application. During a cold start, the system must allocate infrastructure resources, schedule pods, and pull the application image. A cold start also involves the startup time of the application. The size of the application image and the application startup time are based on the developer or service.

Unlike open source Knative, ASK Knative does not scale the number of instances to zero during off-peak hours. Instead, ASK Knative reserves one instance. The following content describes how reserved instances work:

-   ASK Knative uses burstable instances to replace compute optimized instances during off-peak hours. When requests are received, ASK switches back to compute optimized instances. This mechanism reduces costs during off-peak hours.
-   CPU credits that are accumulated during off-peak hours can be used during peak hours to reduce costs.

For more information about reserved instances, see [Reserved instances](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Knative management/Service management/Reserved instances.md).

## Deploy Knative

You can deploy Knative in ASK clusters.

-   If the version of your ASK cluster is 1.16 or later, you can deploy Knative in the Container Service for Kubernetes \(ACK\) console. For more information, see [Deploy Knative in an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Knative management/Component management/One-click Deploy Knative in an ASK cluster.md).
-   If the version of your ASK cluster is earlier than 1.16, you must upgrade your cluster before you can deploy Knative.

## Billing

If you use ASK Knative, you are charged for only cloud resources that are used to manage your ASK cluster. The cloud resources include elastic container instances, SLB instances, and NAT gateways. These resources are charged based on corresponding billing rules. For more information, see [Billing]().

