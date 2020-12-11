---
keyword: [registered clusters, associated resources, feature comparison]
---

# Introduction to registered external clusters

In some scenarios, you need to deploy multiple Kubernetes clusters across regions in the cloud or data centers. You can register external Kubernetes clusters in the Container Service for Kubernetes \(ACK\) console. This allows you to manage multiple clusters in the console and reduce operations and maintenance \(O&M\) costs. This topic describes the features and the associated resources that are supported by registered clusters.

## Benefits of registered clusters

In some scenarios, you need to deploy multiple Kubernetes clusters across regions in the cloud and data centers. The observability of these clusters may vary based on the implementations of cluster auditing, log collection, and monitoring and alerting. In this case, the different levels of observability may make changes to your applications in the clusters. As a result, centralized management of clusters has been a demanding task.

To fix this issue, you can register external clusters in the ACK console and manage these clusters in the console.

## Comparison of features between registered clusters and ACK clusters

|Category|Feature|ACK cluster|Registered cluster|
|--------|-------|-----------|------------------|
|Access to cluster|Accesses clusters through kubectl and kubeconfig files|✔️|✔️|
|Accesses clusters through the console|✔️|✔️|
|Core Kubernetes concepts|Manages namespaces|✔️|✔️|
|Manages pods|✔️|✔️|
|Manages workloads|✔️|✔️|
|Manages persistent volumes \(PVs\) and storage classes|✔️ Cloud disks, local disks, Network Attached Storage \(NAS\) file systems, Cloud Paralleled File System \(CPFS\) file systems, and Object Storage Service \(OSS\) buckets are supported.

|✔️ The supported storage types vary based on the environments in which registered clusters run. |
|Manages Services|✔️|✔️|
|Manages Ingresses|✔️|✔️|
|Manages network policies|✔️ The Terway network plug-in is supported.

|✔️ The support for network policies varies based on the environments in which registered clusters run. |
|Manages ConfigMaps|✔️|✔️|
|Manages Secrets|✔️|✔️|
|Manages role-based access control \(RBAC\)|✔️|✔️|
|Manages pod security policies|✔️|✔️|
|Manages resource quotas|✔️|✔️|
|Manages CustomResourceDefinitions \(CRDs\)|✔️|✔️|
|Supports Horizontal Pod Autoscaling \(HPA\)|✔️|✔️|
|Supports pod RuntimeClasses|✔️ Only sandboxed containers are supported.

|N/A|
|Supports elastic container instances \(ECIs\)|✔️|✔️ For more information about the configurations, see [Add a virtual node to an external cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Add a virtual node to an external cluster.md). |
|Application management|Deploys and manages applications by using Helm charts|✔️|✔️|
|Supports the GitOps-based application delivery chain|✔️|N/A This feature will soon be supported. |
|Supports the Knative add-on|✔️|✔️|
|Supports the Istio add-on|✔️|✔️|
|Supports the fully-managed service mesh platform Alibaba Cloud Service Mesh \(ASM\)|✔️|✔️ For more information about the configurations, see [Use ASM to manage applications in registered external Kubernetes clusters](). |
|Security service|Supports Resource Access Management \(RAM\)-based authentication and RBAC-based authorization|✔️|✔️|
|Supports cluster auditing based on Log Service|✔️|✔️|
|Supports Security Center|✔️|N/A|
|Observability|Supports Event Center that enables the Node Problem Detector \(NPD\) add-on|✔️|✔️ For more information about the configurations, see [Create and use a Kubernetes event center](/intl.en-US/Application/K8s Event Center/Create and use a Kubernetes event center.md). |
|Supports Ingress charts|✔️|✔️ For more information about the configurations, see [Monitor and analyze the logs of nginx-ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Monitor and analyze the logs of nginx-ingress.md). |
|Supports log collection|✔️|✔️ For more information about the configurations, see [Enable Log Service for an external Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Enable Log Service for an external Kubernetes cluster.md). |
|Supports Application Real-Time Monitoring Service \(ARMS\)|✔️|✔️ For more information about the configurations, see [Enable ARMS for an external Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Enable ARMS for an external Kubernetes cluster.md). |
|Supports ARMS Prometheus monitoring|✔️|✔️ For more information about the configurations, see [Enable ARMS Prometheus for an external Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Enable ARMS Prometheus for an external Kubernetes cluster.md). |
|Supports architecture characteristics discovery provided by Application High Availability Service \(AHAS\)|✔️|✔️ N/A |
|Supports application throttling provided by AHAS|✔️|✔️ N/A |
|Supports the NPD add-on|✔️|✔️ For more information about the configurations, see [Enable NPD in an external cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Enable NPD in an external cluster.md). |
|Supports the metrics adapter feature|✔️|✔️ For more information about the configurations, see [Deploy alibaba-cloud-metrics-adapter in an external Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Deploy alibaba-cloud-metrics-adapter in an external Kubernetes cluster.md). |
|Integrates with Cloud Monitor|✔️|N/A|
|Integrates with Key Management Service \(KMS\)|✔️|N/A|
|Manages cluster lifecycle|Manages nodes|✔️|✔️|
|Manages node pools|✔️|N/A|
|Supports cluster auto scaling|✔️|✔️ The support for this feature varies based on the environments in which registered clusters run. |
|Supports certificate rotation|✔️|N/A|
|Supports cluster upgrades|✔️|N/A|
|Manages system components|✔️|N/A|
|Supports cluster checks|✔️|N/A|

## Associated resources

A registered cluster is associated with the following basic resources:

-   [One elastic network interface \(ENI\)](/intl.en-US/Network/Elastic Network Interfaces/ENI overview.md)
-   One guaranteed-performance Server Load Balancer \(SLB\) instance of the slb.s1.small type. For more information, see [Specification](/intl.en-US/User Guide/Instance/SLB instance overview.md).
-   One elastic IP address \(EIP\). For more information, see [Pay-as-you-go](/intl.en-US/Pricing/Pay-as-you-go.md).

A registered cluster can also be associated with other services that are charged based on the features that are used.

## Register external clusters

For more information about how to register external clusters, see [Register an external Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Register an external Kubernetes cluster.md).

