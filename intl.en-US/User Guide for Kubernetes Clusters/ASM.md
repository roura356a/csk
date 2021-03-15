# ASM

You can deploy applications from a Container Service for Kubernetes \(ACK\) cluster to Alibaba Cloud Service Mesh \(ASM\). This simplifies service management by using service meshes, makes services observable, and reduces the workload of development and operations and maintenance \(O&M\).

ASM is a fully managed service mesh platform. ASM is compatible with the open source Istio service mesh. ASM allows you to manage services in a simplified manner. For example, you can use ASM to route and split inter-service traffic, secure inter-service communication with authentication, and observe the behavior of services in meshes. ASM provides the following features for you to manage application services: traffic management, security management, fault recovery, observability and monitoring, and microservices architectures.

## Architecture

The following figure shows the architecture of ASM.

![什么是服务网格](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1789623061/p139995.png)

ASM integrates and manages all components on the Istio control plane to simplify your use of ASM. This way, you can focus on application development and deployment. In addition, ASM is compatible with the Istio community. You can use declarative parameters to define flexible routing rules, and centrally manage traffic between services in a mesh.

An ASM instance with the managed control plane supports application services from multiple Kubernetes clusters or application services that run in elastic container instance pods. You can also deploy non-Kubernetes services, such as services that run on virtual machines or bare metal hosts, into the same service mesh.

**Note:** Only authorized users can add Serverless Kubernetes clusters and Elastic Compute Service \(ECS\) instances to ASM instances. To obtain the required permissions, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

## Features

ASM builds the managed and unified service mesh capabilities in core scenarios, such as hybrid cloud, multi-cloud, multi-cluster, and non-containerized application migration. ASM provides you with the following benefits:

-   Centralized management mode

    ASM manages application services that run in managed, dedicated, and serverless clusters of Container Service for Kubernetes \(ACK\) and registered clusters in hybrid cloud and multi-cloud environments in a centralized manner. This provides unified observability and throttling for application services.

-   Centralized traffic management

    ASM centrally manages the traffic between application services that are deployed in a hybrid environment where containers and virtual machines coexist.

-   Managed core components of the control plane

    ASM manages core components of the Istio control plane, which helps minimize your resource overhead and O&M costs.


## Related operations

For more information about how to get started with ASM, see [Overview]().

