---
keyword: [registered clusters, associated resources, feature comparison]
---

# Introduction to registering external clusters

You can register external Kubernetes clusters in the Container Service for Kubernetes \(ACK\) console, and then centrally manage all clusters in ACK. This reduces the management and maintenance costs. This topic describes the features that are supported by registered clusters and the resources that are associated with a registered cluster.

## Benefits of registering external clusters in ACK

In some scenarios, you may need to manage Kubernetes clusters that are deployed on Alibaba Cloud and in data centers, or manage Kubernetes clusters that are deployed on the clouds of different service providers. After you register external clusters in the ACK console, you can centrally manage and use these clusters. In addition, you can configure cluster auditing, log collection, and monitoring and alerting for external clusters that are registered in ACK.

## Feature comparison between registered clusters and ACK clusters

|Category|Feature|ACK cluster|Registered cluster|
|--------|-------|-----------|------------------|
|Cluster access|Access clusters by using kubectl and kubeconfig files|✔️|✔️|
|Access clusters through the ACK console|✔️|✔️|
|Core Kubernetes concepts|Manage namespaces|✔️|✔️|
|Manage pods|✔️|✔️|
|Manage workloads|✔️|✔️|
|Manage persistent volumes \(PVs\) and storage classes|✔️ Alibaba Cloud disks, local disks, Apsara File Storage NAS \(NAS\) file systems, Cloud Paralleled File System \(CPFS\) file systems, and Object Storage Service \(OSS\) buckets are supported.

|✔️ The supported storage types vary based on the environments in which registered clusters run. |
|Manage Services|✔️|✔️|
|Manage Ingresses|✔️|✔️|
|Manage network policies|✔️ The Terway network plug-in is supported.

|✔️ The support for network policies varies based on the environments in which registered clusters run. |
|Manage ConfigMaps|✔️|✔️|
|Manage Secrets|✔️|✔️|
|Manage role-based access control \(RBAC\)|✔️|✔️|
|Manage pod security policies|✔️|✔️|
|Manage resource quotas|✔️|✔️|
|Manage Custom Resource Definitions \(CRDs\)|✔️|✔️|
|Horizontal Pod Autoscaler \(HPA\)|✔️|✔️|
|Pod RuntimeClasses|✔️ Only sandboxed containers are supported.

|No.|
|Elastic container instances|✔️|✔️ For more information about the configurations, see [Add a virtual node to an external cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Add a virtual node to an external cluster.md). |
|Application management|Deploy and manage applications by using Helm charts|✔️|✔️|
|GitOps-based application delivery chains|✔️|No. This feature will soon be supported. |
|The Knative add-on|✔️|✔️|
|The Istio add-on|✔️|✔️|
|Alibaba Cloud Service Mesh \(ASM\), a fully-managed service mesh platform|✔️|✔️ For more information about the configurations, see [Use ASM to manage applications in registered external Kubernetes clusters](). |
|Security service|Resource Access Management \(RAM\)-based authentication and RBAC-based authorization|✔️|✔️|
|Cluster auditing with Log Service|✔️|✔️|
|Security Center|✔️|No.|
|Observability|Event Center \(node problem detection\)|✔️|✔️ For more information about the configurations, see [Create and use a Kubernetes event center](/intl.en-US/Application/K8s Event Center/Create and use a Kubernetes event center.md). |
|Ingress charts|✔️|✔️ For more information about the configurations, see [Monitor and analyze the logs of nginx-ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Monitor and analyze the logs of nginx-ingress.md). |
|Log collection|✔️|✔️ For more information about the configurations, see [Enable Log Service for an external Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Enable Log Service for an external Kubernetes cluster.md). |
|Application Real-Time Monitoring Service \(ARMS\)|✔️|✔️ For more information about the configurations, see [Enable ARMS for an external Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Enable ARMS for an external Kubernetes cluster.md). |
|ARMS Prometheus monitoring|✔️|✔️ For more information about the configurations, see [Enable ARMS Prometheus for an external Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Enable ARMS Prometheus for an external Kubernetes cluster.md). |
|Architecture characteristics discovery provided by Application High Availability Service \(AHAS\)|✔️|✔️ No. |
|Application throttling provided by AHAS|✔️|✔️ No. |
|Node Problem Detector \(NPD\)|✔️|✔️ For more information about the configurations, see [Create a Kubernetes event center for an external Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Create a Kubernetes event center for an external Kubernetes cluster.md). |
|Metrics adapter|✔️|✔️ For more information about the configurations, see [Deploy alibaba-cloud-metrics-adapter in an external Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Deploy alibaba-cloud-metrics-adapter in an external Kubernetes cluster.md). |
|Cloud Monitor integration|✔️|No.|
|Key Management Service \(KMS\) integration|✔️|No.|
|Manage cluster lifecycle|Manage nodes|✔️|✔️|
|Manage node pools|✔️|No.|
|Cluster auto scaling|✔️|✔️ The support for this feature varies based on the environments in which registered clusters run. |
|Certificate rotation|✔️|No.|
|Cluster upgrades|✔️|No.|
|Manage system components|✔️|No.|
|Cluster health checks|✔️|No.|

## Associated resources

A registered cluster is associated with the following basic resources:

-   [An elastic network interface \(ENI\)](/intl.en-US/Network/Elastic Network Interfaces/ENI overview.md).
-   A high-performance Server Load Balancer \(SLB\) instance of the slb.s1.small type. For more information, see [Specification](/intl.en-US/Classic Load Balancer/User Guide/Instance/SLB instance overview.md).
-   An elastic IP address \(EIP\). For more information, see [Pay-as-you-go](/intl.en-US/Pricing/Pay-as-you-go.md).

A registered cluster can be associated with other services. These services are charged based on the actual use.

## Register an external cluster

For more information about how to register an external cluster, see [Register an external Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Register an external Kubernetes cluster.md).

