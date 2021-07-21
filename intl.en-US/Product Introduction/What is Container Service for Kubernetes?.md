---
keyword: [ACK, Kubernetes, Container Service, Container Service for Kubernetes]
---

# What is Container Service for Kubernetes?

Container Service for Kubernetes \(ACK\) is one of the first services to pass the Certified Kubernetes Conformance Program. ACK provides high-performance management services for containerized applications. You can manage enterprise-level containerized applications throughout the application lifecycle. This service allows you to run containerized applications in the cloud in an efficient manner.

## Cluster types

ACK provides the following three cluster types: dedicated Kubernetes clusters, managed Kubernetes clusters, and serverless Kubernetes clusters.

|Item|Dedicated Kubernetes cluster|Managed Kubernetes cluster|Serverless Kubernetes|
|----|----------------------------|--------------------------|---------------------|
|Feature|You must create master nodes and worker nodes.|You only need to create worker nodes. ACK creates and manages master nodes.|You do not need to create master nodes or worker nodes.|
|A dedicated Kubernetes cluster allows you to manage the cluster infrastructure in a more fine-grained manner. You must design, maintain, and upgrade the Kubernetes cluster on your own.|A managed Kubernetes cluster is easy-to-use, cost-efficient, and highly available. You do not need to manage master nodes.|A serverless Kubernetes cluster allows you to start applications directly. You do not need to manage nodes.|
|Billing method|You are not billed for cluster management features. However, you must pay for master nodes, worker nodes, and other infrastructure.|-   Standard ACK clusters: You are not billed for cluster management features. However, you must pay for worker nodes and other infrastructure.
-   [Professional ACK clusters](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Introduction to professional managed Kubernetes clusters.md): You are billed based on the number of clusters or the subscription method.

|You are billed for the resources consumed by pods and the resource usage duration. The duration is measured in seconds.|
|Use scenario|Applies to all scenarios.|Applies to all scenarios.|Applies to batch tasks, urgent application scale-out, and continuous integration \(CI\) or continuous delivery \(CD\).|
|User profile|![Dedicated Kubernetes cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3952183061/p143465.png)

|![2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3952183061/p143466.png)

|![3](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3952183061/p143467.png) |
|Cluster creation procedure|![Dedicated Kubernetes cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3952183061/p143471.png)

|![Managed Kubernetes cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3952183061/p143472.png)

|![ASK](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3952183061/p143473.png) |

## Features

-   Cluster management
    -   Create clusters: You can create multiple types of cluster based on your requirements, choose multiple types of worker node, and flexibly customize the configurations.
    -   Upgrade clusters: You can upgrade Kubernetes with a few clicks and manage the upgrade of system components in a unified manner.
    -   Manage node pools: You can manage the lifecycle of node pools. You can configure node pools of different specifications in a cluster, such as vSwitches, runtimes, operating systems, and security groups.
    -   [Elastic scaling](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Overview.md): You can scale in and scale out resources in the console with a few clicks based on your requirements. You can also use service-level affinity rules and scale up resources.
    -   [Manage multiple clusters](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Overview of the multi-cloud and hybrid cloud solution.md): You can manage applications in data centers and clusters in multiple clouds and regions in a unified manner.
    -   Manage permissions: You can grant permissions to users in the RAM console or by using role-based access control \(RBAC\) policies.
-   Application management
    -   Create applications: You can create multiple types of application based on images and templates. You can configure environment variables, application health checks, data disks, and logging.
    -   Manage lifecycles: You can view, update, and delete applications, roll back application versions, view application events, perform rolling updates of applications, use new application versions to replace old application versions, and use triggers to redeploy applications.
    -   Schedule applications: You can schedule application pods based on the following three policies: pod affinity, node affinity, and pod anti-affinity.
    -   Scale applications: You can scale the number of application pods manually or by using the Horizontal Pod Autoscaler \(HPA\).
    -   Release applications: Phased release and blue-green release are supported.
    -   App Catalog: You can use App Catalog to simplify the integration of Alibaba Cloud services.
    -   [Application center](/intl.en-US/User Guide for Kubernetes Clusters/Application center/Overview.md): After an application is deployed, the application center displays the topology of the application on one page. You can also manage and roll back the application version in scenarios such as continuous deployment.
    -   Back up and restore applications: You can back up Kubernetes applications and restore applications from backup data. For more information, see [Back up and restore applications in Kubernetes clusters]().
-   Storage and networking
    -   [Storage plug-ins](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Storage overview.md): FlexVolume and CSI are supported.
    -   Volumes and persistent volume claims \(PVCs\):
        -   You can create Block Storage volumes, Apsara File Storage NAS \(NAS\) volumes, Object Storage Service \(OSS\) volumes, and Cloud Paralleled File System \(CPFS\) volumes.
        -   You can bind a volume to a PVC.
        -   You can dynamically create and migrate volumes.
        -   You can view and update volumes and PVCs by running scripts.
    -   [Network](/intl.en-US/User Guide for Kubernetes Clusters/Network/Overview.md):
        -   You can create clusters in virtual private clouds \(VPCs\) and use the Flannel or Terway network plug-ins.
        -   You can specify CIDR blocks of Services and pods.
        -   You can use the NetworkPolicy feature.
        -   You can use Ingresses to route requests from outside a cluster to services within the cluster.
-   O&M and security
    -   Monitoring: You can monitor clusters, nodes, applications, and pods. You can use the Prometheus plug-in.
    -   Logging: You can view cluster logs, pod logs, and application logs.
    -   The [Runtime Security](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Security/Use Runtime Security to monitor ACK clusters and configure alerts.md) page allows you to manage security policies of the container runtime, configure routine inspections of application security, and configure security monitoring and alerting on the runtime. This enhances the overall security capabilities of containers.
    -   [Sandboxed-Container](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Sandboxed-Container overview.md) allows you to run an application in a sandboxed and lightweight virtual machine. This virtual machine has a dedicated kernel, isolates applications from each other, and provides enhanced security. Sandboxed-Container is suitable in scenarios such as untrusted application isolation, fault isolation, performance isolation, and load isolation among multiple users.
    -   [TEE-based confidential computing](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/TEE-based confidential computing.md) is a cloud-native and all-in-one solution based on Intel Software Guard Extensions \(SGX\). This solution ensures data security, integrity, and confidentiality. Confidential computing allows you to isolate sensitive data and code by using a trusted execution environment.

## Architecture

The following figure shows the architecture of the Alibaba Cloud Container Service product portfolio.

![Architecture](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3768516261/p169914.png)

-   [Alibaba Cloud Container Registry]() provides managed security services and lifecycle management of cloud-native assets. The service distributes images to clusters in different scenarios and is seamlessly integrated with ACK to provide an all-in-one solution for cloud-native application management.
-   [Alibaba Cloud Service Mesh \(ASM\)]() is a managed service mesh platform that allows you to manage the traffic of an application that uses the microservices architecture in a unified manner. ASM is compatible with the open source Istio service mesh platform and allows you to manage the traffic of multiple Kubernetes clusters. ASM provides a unified way to manage the communications among containerized applications and applications on virtual machines.
-   [Alibaba Cloud Serverless Kubernetes \(ASK\)](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Introduction.md) provides serverless Kubernetes clusters based on elastic computing. You can create containerized applications without managing or maintaining clusters.
-   [Alibaba Cloud Genomics Service \(AGS\)](/intl.en-US/User Guide for Genomics Service/Overview.md) is a genome sequencing and secondary analysis service based on big data. It serves biotechnology industry users. AGS is an efficient, elastic, and reliable service that requires low costs.
-   [Alibaba Cloud Container Service for Kubernetes@Edge \(ACK@Edge\)](/intl.en-US/User Guide for Edge Container Service/ACK@Edge overview.md) is a Kubernetes cluster based on the standard Kubernetes runtime environment. It integrates the cloud, edge, and terminals to deliver, maintain, and manage applications. ACK@Edge also enhances node autonomy in edge clusters.

## Use ACK

Click the following button to start using ACK.

## References

[Kubernetes official website](https://kubernetes.io/zh/docs/concepts/overview/what-is-kubernetes/)

