# Knative overview {#concept_525881 .concept}

This topic describes the concept of Knative, roles involved in a Knative system, Knative components, and thirty-party add-on supported by Knative.

## Background information {#section_brq_7yi_4tp .section}

Knative is a serverless framework that is based on Kubernetes. The goal of Knative is to provide the cloud-native standard to orchestrate serverless workloads across different platforms. To implement this goal, Knative codifies the best practices around three areas of developing cloud native applications: building container and function, serving and dynamically scaling workloads, and eventing.

## Roles involved in the Knative system {#section_aux_v9c_mwo .section}

-   Developers: refers to personnel that directly use native Kubernetes APIs to deploy serverless functions, applications, and containers to an auto-scaling runtime.
-   Contributors: refers to personnel that develop and contribute code and documents to the Knative community.
-   Operators: refers to personnel that deploy and manage Knative instances by using Kubernetes APIs and tools. Knative can be integrated into any environments that support Kubernetes, such as systems of any enterprises or cloud providers.
-   Users: refers to personnel that use an Istio gateway to access the target services, or use the eventing system to trigger the serverless service of Knative.

For more information, see [Personas involved in Knative](https://knative.dev/docs/).

## Components {#section_j5m_9xp_ari .section}

Knative consists of the following three components:

 Build
 :   This component is used to obtain the source code of an application from a code repository, compile the code into container images, and then push the images to an image repository. All these actions occur in the corresponding Kubernetes pods.

  Eventing
 :   This component can be used to manage and deliver events.

 :   Specifically, Eventing is designed to provide an event model to drive serverless events, including how to connect to an external event source, register and subscribe events, and filter events. The event model decouples event producers and event consumers. This means that any producer can generate events before a consumer starts to listen to the events, and any event consumer can listen events before producers start to generate the events.

  Serving
 :   This component can be used to manage serverless workloads. It provides the request-driven capability to auto scale workloads. If no request is needed to be processed, Serving can help to scale workload instances to zero instance. For advanced scenarios, workload instances can be scaled to any required number without limitation. In addition, this component supports the function of granary deployment.

 ## Third-party add-on {#section_8ap_0bm_nwy .section}

Knative supports the GitHub add-on for using the GitHub event source.

