---
keyword: [Istio, microservices]
---

# Overview

Istio is an open platform that allows you to connect, secure, control, and observe microservices. This topic describes the features and architecture of Istio.

## Microservices

Microservices are widely adopted by IT enterprises. A microservice architecture allows you to develop an application that consists of multiple independent services. In this case, you can develop, deploy, and scale each service without affecting other services. You can run microservices on pods. This simplifies the delivery of microservices and improves the reliability and scalability of applications.

The distributed architecture of microservices challenges application management, including application O&M, debugging, and security management. Developers must operate and maintain complex services, such as service discovery, load balancing, fault recovery, metric collection and monitoring, A/B testing, phased releases, blue-green releases, traffic throttling, access control, and service-to-service authentication.

## Istio overview

To address the preceding issues, Istio comes into being. Istio is an open platform that allows you to connect, secure, control, and observe microservices. It provides a simple way to build microservice networks. You can use Istio to enable load balancing, service-to-service authentication, and service monitoring without changing service code.

Istio enables the following features:

-   Traffic management: controls traffic and API operations between services. This feature enhances system reliability.
-   Authentication and security protection: enables service-to-service authentication in a mesh and secures service traffic. This feature enhances system security.
-   Policy enforcement: controls access policies between services. You do not have to modify services.
-   Observability: obtains traffic distribution and API calls between services. This feature helps you troubleshoot issues at the earliest opportunity after they occur.

## Istio architecture

An Istio service mesh is logically split into a data plane and a control plane.

-   The control plane manages proxies to route traffic and enforce policies at runtime. By default, Istio uses Envoy proxies.
-   The data plane consists of a set of proxies that manage and control network communication between services. By default, Istio uses Envoy proxies.

![Istio architecture](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7555359951/p10963.png)

The following list describes the core components of Istio:

-   Pilot: collects and validates configurations, and propagates them to individual components of Istio. Pilot extracts environment-specific implementation details from Mixer and Envoy and provides an abstract representation of user services for Mixer and Envoy. The services are independent of the underlying platform. You can also use Pilot to program traffic management rules, such as generic Layer 4 rules and Layer 7 HTTP or gRPC routing rules.
-   Mixer: enforces access control and usage policies across a mesh and collects telemetry data from Envoy and other services. Mixer enforces policies based on the attributes provided by Envoy.
-   Security: enables service-to-service and user-to-user authentication. You can use this feature to ensure security between services without changing service code. Security in Istio involves the following three components:
    -   Identity: identifies the principal that runs services based on the service account provided by Container Service for Kubernetes \(ACK\).

    -   Key management: provides a Certificate Authority \(CA\) to enable automatic generation and management of keys and certificates.

    -   Communication security: provides a tunnel between the client and the server through Envoy to secure services.

-   Envoy: works as an independent proxy that is deployed on the same pod that runs the microservice and provides a set of attributes for Mixer. Mixer enforces policies based on the attributes, and sends them to monitoring systems.

**Related topics**  


[Deploy Istio](/intl.en-US/User Guide for Kubernetes Clusters/Service mesh/Istio management/Deploy Istio.md)

[](/intl.en-US/User Guide for Kubernetes Clusters/Service mesh/Istio management/Update Istio.md)

[Delete Istio](/intl.en-US/User Guide for Kubernetes Clusters/Service mesh/Istio management/Delete Istio.md)

