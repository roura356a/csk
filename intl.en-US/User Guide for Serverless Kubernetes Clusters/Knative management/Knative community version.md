# Knative community version

Knative is a Kubernetes-based framework for serverless applications. Knative is designed to create a cloud-native, cross-platform orchestration standard for serverless applications. Knative implements this standard by integrating the creation of containers \(or functions\), workload management \(automatic scaling\), and event models.

## Knative architecture

The following figure shows how different roles interact with each other in the Knative architecture.

![Knative architecture](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4703121061/p48848.png)

-   Developers

    Developers of serverless applications can call the native Kubernetes API to deploy serverless applications based on Knative.

-   Contributors

    Contributors in the preceding figure mainly refer to the contributors to the Knative community.

-   Operators

    Knative can be integrated with environments that are supported by Knative, such as environments of cloud service providers or internal environments of enterprises. Knative is based on Kubernetes. This means that Knative can be deployed in the environments where Kubernetes is implemented.

-   Users

    Users access services through Istio gateways, or launch serverless applications by triggering Knative events.


## Core Knative components

Knative consists of three core components. These components form a general-purpose serverless framework:

-   Tekton: provides the capability to create images from source code.

    Tekton is used to retrieve source code from the code repository, compile the code into images, and push the images to the image repository. All these operations are performed in Kubernetes pods.

-   Knative Eventing: provides event management capabilities, such as producing and consuming events.

    Knative Eventing has designed an all-in-one eventing system for event-driven serverless applications. The eventing system provides features such as the installation of external event sources, registration and subscription of events, and filtering of events. The event system decouples event producers and event consumers. An event producer can generate events before active event consumers listen to events. An event consumer can listen to events before active producers produce events.

-   Knative Serving: manages the workloads of serverless applications. Knative Serving enables automatic scaling for pods where serverless applications are deployed based on Knative events and user requests. If no workload is processed, the number of pods is scaled to zero.

    Knative Serving is used to manage the workloads of serverless applications that provide services to users. The most important feature of Knative Serving is automatic scaling. The scaling capacity is not limited. Knative Serving also supports phased release.


