# Overview

Knative is a Kubernetes-based serverless framework. Knative creates a cloud-native and cross-platform orchestration standard for serverless applications. Knative enforces this standard by streamlining the creation of container or function, workload management and auto scaling, and event models.

## Knative architecture

The following figure shows how different roles interact with each other in the Knative architecture.

![Knative architecture](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4703121061/p48848.png)

-   Developers

    Developers of serverless applications can call the Kubernetes-native API to deploy serverless applications based on Knative.

-   Contributors

    Contributors in the preceding figure mainly refer to the contributors to the Knative community.

-   Operators

    Knative can be integrated with environments that are supported by Knative, such as environments of cloud service providers or internal environments of enterprises. Knative is based on Kubernetes. Therefore, it can be deployed in environments where Kubernetes is implemented.

-   Users

    Users access services through Istio gateways, or run serverless applications by triggering Knative events.


## Core Knative components

Knative consists of three core components. These components form a general-purpose serverless framework:

-   Tekton: provides the capability to create images from source code.

    Tekton is used to retrieve source code from the code repository, compile the code into images, and push the images to the image repository. All these operations are performed in Kubernetes pods.

-   Knative Eventing: provides event management capabilities, such as producing and consuming events.

    Knative Eventing has designed an all-in-one eventing system for event-driven serverless applications. The eventing system provides features that allow you to install external event sources, register and subscribe to events, and filter events. The event system decouples event producers and event consumers. An event producer can generate events before active event consumers listen to events. An event consumer can listen to events before active producers produce events.

-   Knative Serving: manages the workloads of serverless applications. Knative Serving enables automatic scaling for pods where serverless applications are deployed based on Knative events and user requests. If no workload is processed, the number of pods is scaled to zero.

    Knative Serving is used to manage the workloads of serverless applications that provide services to users. The most important feature of Knative Serving is automatic scaling. The scaling capacity is not limited. Knative Serving also supports canary release.


## Knative add-ons

Knative supports third-party add-ons. The GitHub add-on component is supported. This component provides support for GitHub event sources.

## Deploy Knative components in a Kubernetes cluster

Knative components can be deployed in standard managed Kubernetes clusters, standard dedicated Kubernetes clusters, and serverless Kubernetes \(ASK\) clusters. You can deploy Knative components by using the following methods:

-   To deploy Knative components in ACK standard managed clusters and standard dedicated ACK clusters, see [Deploy Knative](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative components/Deploy Knative.md).
-   You can deploy Knative components in serverless Kubernetes clusters. For more information, see [Deploy Knative in an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Knative management/Component management/One-click Deploy Knative in an ASK cluster.md).

    **Note:** Make sure that the version of the cluster is 1.16 or later.


## Knative billing rules

Knative is free of charge. However, you are charged for the cloud resources that are created during the use of Knative. For example, you may create ECS instances, SLB instances, and NAT gateways. You are charged for creating and using these resources. For more information about the billing methods of these resources, see the following topics:

-   [ECS billing overview](/intl.en-US/Pricing/Billing overview.md)
-   [Billing overview](/intl.en-US/Pricing/Billing overview.md)

**Related topics**  


[Deploy Knative](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative components/Deploy Knative.md)

[t1040500.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative services/Create a Knative Service.md)

