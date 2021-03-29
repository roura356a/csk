---
keyword: [registered clusters, involved resources, feature comparison]
---

# Overview of registered clusters

Container Service for Kubernetes \(ACK\) allows you to create a cluster registration proxy and use the proxy to register a Kubernetes cluster that is deployed in a data center or on a third-party cloud. This allows you to manage your clusters in the ACK console in a centralized manner. This topic describes the features that are supported by registered clusters and the resources that are involved when you register clusters.

## Benefits of registered external clusters in ACK

Your daily operations and maintenance process may involve ACK clusters, self-managed Kubernetes clusters deployed in data centers, and Kubernetes clusters deployed on third-party clouds. If multiple Kubernetes clusters are deployed in a multi-cloud or hybrid cloud architecture and you have the following requirements, we recommend that you register external clusters in ACK:

-   Use the ACK console to centrally manage ACK clusters and self-managed Kubernetes clusters that are deployed in data centers. The ACK console provides the same O&M experience and security governance ability across the clusters. For example, the ACK console provides a unified logging, monitoring, and alerting system, and supports unified authorization management of Alibaba Cloud accounts and RAM users. This allows you to centrally manage all your cluster and applications.
-   Use cloud computing resources to scale out self-managed Kubernetes clusters that are deployed in data centers. For example, manually or automatically adjust the number of Elastic Compute Service \(ECS\) instances or ECS Bare Metal instances in self-managed Kubernetes clusters that are deployed in data centers.
-   Use the Application Center feature of ACK to centrally manage application lifecycles and GitOps delivery pipelines for all cloud and on-premises Kubernetes clusters. To achieve this goal, you must register on-premises Kubernetes clusters in the ACK console so that you can manage all Kubernetes clusters by using the same control plane.
-   Use Alibaba Cloud Service Mesh \(ASM\) to implement centralized service governance for all cloud and on-premises Kubernetes clusters. To achieve this goal, you must register on-premises Kubernetes clusters in the ACK console so that you can manage all Kubernetes clusters by using the same control plane.

## Comparison of features supported by registered clusters and ACK clusters

|Category|Feature|ACK cluster|Registered cluster|
|--------|-------|-----------|------------------|
|Cluster access|Access clusters by using kubectl and kubeconfig files|✔️|✔️|
|Access clusters by using the ACK console|✔️|✔️|
|Core Kubernetes concepts|Manage namespaces|✔️|✔️|
|Manage pods|✔️|✔️|
|Manage workloads|✔️|✔️|
|Manage persistent volumes \(PVs\) and storage classes|✔️ Supports Alibaba Cloud disks, local disks, Apsara File Storage NAS \(NAS\) file systems, Cloud Paralleled File System \(CPFS\) file systems, and Object Storage Service \(OSS\) buckets.

|✔️ The supported storage types vary based on the environment in which the registered cluster is deployed. |
|Manage Services|✔️|✔️|
|Manage Ingresses|✔️|✔️|
|Manage network policies|✔️ Supports the Terway network plug-in.

|✔️ The supported network policies vary based on the environment in which the registered cluster is deployed. |
|Manage ConfigMaps|✔️|✔️|
|Manage Secrets|✔️|✔️|
|Manage role-based access control \(RBAC\)|✔️|✔️|
|Manage security policies of pods|✔️|✔️|
|Manage resource quotas|✔️|✔️|
|Manage Custom Resource Definitions \(CRDs\)|✔️|✔️|
|Horizontal Pod Autoscaler \(HPA\)|✔️|✔️|
|Pod RuntimeClasses|✔️ Supports only sandboxed containers.

|Not supported|
|Elastic container instances|✔️|✔️ For more information about the configurations, see [Add a virtual node to an external cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Add a virtual node to an external cluster.md). |
|Application management|Deploy and manage applications by using Helm charts|✔️|✔️|
|GitOps application delivery|✔️|Not supported|
|Knative add-on|✔️|✔️|
|Istio add-on|✔️|✔️|
|Alibaba Cloud Service Mesh \(ASM\)|✔️|✔️ For more information about the configurations, see [Use ASM to manage applications in registered external Kubernetes clusters](). |
|Security governance|Resource Access Management \(RAM\)-based authentication and RBAC-based authorization|✔️|✔️|
|Cluster auditing by using Log Service|✔️|✔️|
|Security Center|✔️|Not supported|
|Observability|Event Center \(node problem detection\)|✔️|✔️ For more information about the configurations, see [Create and use a Kubernetes event center](/intl.en-US/Application/K8s Event Center/Create and use a Kubernetes event center.md). |
|Ingress charts|✔️|✔️ For more information about the configurations, see [Monitor and analyze the logs of nginx-ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Monitor and analyze the logs of nginx-ingress.md). |
|Log collection|✔️|✔️ For more information about the configurations, see [Enable Log Service for an external Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Enable Log Service for an external Kubernetes cluster.md). |
|Application Real-Time Monitoring Service \(ARMS\)|✔️|✔️ For more information about the configurations, see [Enable ARMS for an external Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Enable ARMS for an external Kubernetes cluster.md). |
|ARMS Prometheus monitoring|✔️|✔️ For more information about the configurations, see [Enable ARMS Prometheus for an external Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Enable ARMS Prometheus for an external Kubernetes cluster.md). |
|Architecture characteristics discovery provided by Application High Availability Service \(AHAS\)|✔️|✔️ Not supported |
|Application throttling provided by AHAS|✔️|✔️ Not supported |
|Node Problem Detector \(NPD\)|✔️|✔️ For more information about the configurations, see [Create a Kubernetes event center for an external Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Create a Kubernetes event center for an external Kubernetes cluster.md). |
|Metrics adapter|✔️|✔️ For more information about the configurations, see [Deploy alibaba-cloud-metrics-adapter in an external Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Deploy alibaba-cloud-metrics-adapter in an external Kubernetes cluster.md). |
|Integration with Cloud Monitor|✔️|Not supported|
|Integration with Key Management Service \(KMS\)|✔️|Not supported|
|Cluster lifecycle management|Manage nodes|✔️|✔️|
|Manage node pools|✔️|Not supported|
|Cluster auto scaling|✔️|✔️ The support for this feature varies based on the environment in which the registered cluster is deployed. |
|Certificate rotation|✔️|Not supported|
|Cluster upgrades|✔️|Not supported|
|Manage system components|✔️|Not supported|
|Cluster health checks|✔️|Not supported|

## Resources that are involved when you register clusters

When you register an external cluster in the ACK console, the following resources are involved:

-   A virtual private cloud \(VPC\) and a vSwitch. The VPC must have access to the Internet. You can associate the cluster with an elastic IP address \(EIP\) or configure SNAT rules. You are charged for the VPC and vSwitch.
-   [An elastic network interface \(ENI\)](/intl.en-US/Network/Elastic Network Interfaces/ENI overview.md). You are not charged for the ENI.
-   An internal-facing Server Load Balancer \(SLB\) instance. You are charged for the SLB instance based on the instance specification. For more information about the pricing of different instance specifications, see [Instance types and specifications](/intl.en-US/Classic Load Balancer/User Guide/Instance/SLB instance overview.md).
-   An EIP. You are charged for data transfer. For more information about data transfer fees, see [Pay-as-you-go](/intl.en-US/Pricing/Pay-as-you-go.md).

If you want to automatically scale self-managed on-premises Kubernetes clusters by adding or removing cloud computing resources, the following resources are also involved:

-   ECS instances, ECS Bare Metal instances, and elastic container instances. You are charged for these resources based on your actual resource usage.
-   Auto Scaling, which is free of charge.

You are charged other fees based on the features that you use.

