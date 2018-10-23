# Overview {#concept_lnr_x1c_z2b .concept}

Istio is an open platform that provides connection, protection, control and monitors microservices.

Microservices are currently being valued by more and more IT enterprises. Microservices are multiple services divided from a complicated application. Each service can be developed, deployed, and scaled. Combining the microservices and container technology simplifies the delivery of microservices and improves the liability and scalability of applications.

As microservices are extensively used, the distributed application architecture composed of microservices becomes more complicated in dimensions of operation and maintenance, debugging, and security management. Developers have to deal with greater challenges, such as service discovery, load balancing, failure recovery, metric collection and monitoring, A/B testing, gray release, blue-green release, traffic limiting, access control, and end-to-end authentication.

Istio emerged. Istio is an open platform for connecting, protecting, controlling, and monitoring microservices. It provides a simple way to create microservices networks and provides capabilities such as load balancing, inter-service authentication, and monitoring. Besides, Istio can provide the preceding functions without modifying services.

Istio provides the following functions:

-   Traffic management: Controls traffic and API calls between services to enhance the system reliability.
-   Authentication and security protection: Provides authentication for services in meshes, and protects the traffic of services to enhance the system security.
-   Policy execution: Controls access policies between services without requiring changes to the services.
-   Observability: Obtains traffic distribution and call relationships between services to quickly locate problems.

## Istio architecture {#section_ofx_myf_z2b .section}

Istio is logically divided into a control plane and a data plane:

-   Control plane: Administration proxy \(the default is Envoy \) for managing traffic routing, runtime policy execution, and more
-   Data plane: Consists of a series of proxys \(the default is Envoy\) for managing and controlling network communication between services.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/18952/154025845010963_en-US.png)

Istio is composed of the following components:

-   Istio Pilot: Collects and validates configurations, and propagates them to various Istio components. It extracts environment-specific implementation details from the policy execution module \(Mixer\) and the intelligent proxy \(Envoy\), providing them with an abstract representation of user services, independent of the underlying platform. In addition, traffic management rules \(that is, generic Layer-4 rules and Layer-7 HTTP/gRPC routing rules\) can be programmed through Pilot at runtime.
-   Policy execution module \(Mixer\): Executes access control and usage policies across the service mesh, and collects telemetry data from the intelligent proxy \(Envoy\) and other services. Mixer executes policies based on the Attributes provided by the intelligent proxy \(Envoy\).
-   Istio security module: Provides inter-service and inter-user authentication to guarantee enhanced security between services without modifying service codes. Includes three components:
    -   Identification: When Istio runs on Kubernetes, it identifies the principal that runs the service according to the service account provided by container Kubernetes.

    -   Key management: Provides CA automated generation, and manages keys and certificates.

    -   Communication security: Provides a tunnel between the client and the server through the intelligent proxy \(Envoy\) to secure services.

-   Intelligent proxy \(Envoy\): Deployed as an independent component in the same Kubernetes pod along with relevant microservice, and provides a series of attributes to the policy execution module \(Mixer\). The policy execution module \(Mixer\) uses these attributes as the basis to execute policies, and sends them to monitoring systems.

