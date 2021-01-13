# Overview

The Knative community version is a cloud-native and cross-platform orchestration engine for serverless applications. You can deploy the Knative community version in a serverless Kubernetes \(ASK\) cluster. However, you must pay additional fees. ASK is integrated with Knative. To use cloud resources by calling the Knative API, you only need to create an ASK cluster and enable Knative for the cluster. In this case, you do not need to pay additional fees.

## Benefits

|The Knative community version|ASK Knative|
|-----------------------------|-----------|
|By default, Istio gateways are used. Therefore, you must pay for some infrastructures that are used to install Istio controllers.|You do not have to pay for the Knative controller.|
|You must pay extra fees for Infrastructure as a Service \(IaaS\) that is required to install the Knative controller.|
|A cold start time is required before a pod is created in an ASK cluster. The Knative community version uses the scale-to-zero mechanism to reduce the costs. However, during the cold start, the cluster may fail to process requests due to session timeout.|ASK Knative does not scale the number of instances to zero during off-peak hours. Instead, ASK Knative uses reserved instances. ASK Knative uses reserved instances to prevent cold starts at low costs. For more information about reserved instances, see [Reserved instances](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Knative management/Manage Knative services/Reserved instances.md).|

## Resource management in Knative

ASK hosts serverless applications and provides an easy way to use Kubernetes. You can directly deploy containerized applications in ASK clusters without the need to purchase nodes. The following list describes the benefits of the resource management in Knative:

-   You can use Knative to manage applications in ASK clusters.
-   Knative automatically requests pod resources from ASK clusters when it is required.

The Knative Serving controller is integrated in ACK. To use cloud resources by calling the Knative API, you only need to create an ASK cluster and enable Knative for the cluster. You do not need to pay for the Knative controller.

## Knative Gateway

By default, the Knative community version supports multiple gateway solutions, such as Istio, Gloo, Contour, Kourier, and Ambassador. Istio is most frequently used among these solutions. This is because Istio also functions as a service mesh. Each ASK cluster must contain at least two resident gateway instances. The two gateway instances provide backup for each other to ensure high availability. The controllers of these gateways must be resident. You must pay infrastructure and O&M fees for these resident instances.

To improve user experience, Alibaba Cloud provides Knative Gateway based on Server Load Balancer \(SLB\) instances. Knative Gateway provides gateway services required by ASK clusters. Knative Gateway is as stable and reliable as cloud resources. Resident resources are not required. This reduces infrastructure costs and O&M workloads.

## Reserved instances

By default, the Knative community version scales the number of instances to zero during off-peak hours. This reduces the costs of running resident instances. During a cold start, the system must allocate IaaS resources, schedule pods, and pull images. A cold start also involves the startup time of the application. The image size and startup time of the application vary based on developers and services.

Unlike the Knative community version, ASK Knative does not scale the number of instances to zero during off-peak hours. Instead, ASK Knative reserves one instance. The following content describes how reserved instances work:

-   ASK Knative uses burstable instances to replace compute optimized instances during off-peak hours. When requests are received, ASK switches back to compute optimized instances. This mechanism reduces the computing costs during off-peak hours.
-   CPU credits that are accumulated during off-peak hours can be used during peak hours to reduce the computing costs.

For more information about reserved instances, see [Reserved instances](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Knative management/Manage Knative services/Reserved instances.md).

## Deploy Knative

You can deploy Knative in ASK clusters.

-   If the version of your ASK cluster is V1.16 or later, you can deploy Knative in the ACK console. For more information, see [Deploy Knative in an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Knative management/Manage Knative components/One-click Deploy Knative in an ASK cluster.md).
-   If the version of your ASK cluster is earlier than V1.16, you must join the Knative DingTalk group to deploy Knative:
    1.  Scan the following QR code to join the Knative DingTalk group.

        ![Knative DingTalk group](../images/p135061.png)

    2.  Send the cluster ID to the group owner to deploy Knative in the cluster.

