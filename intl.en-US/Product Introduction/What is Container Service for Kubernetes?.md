---
keyword: [ACK, Kubernetes, Container Service, Container Service for Kubernetes]
---

# What is Container Service for Kubernetes?

Container Service for Kubernetes \(ACK\) is one of the first services to participate in the Certified Kubernetes Conformance Program. ACK provides high-performance management services of containerized applications. You can manage enterprise-level containerized applications throughout the application lifecycle. This service allows you to run containerized applications in the cloud in an efficient manner.

## Cluster types

ACK provides the following three types of Kubernetes clusters: dedicated Kubernetes clusters, managed Kubernetes clusters, and serverless Kubernetes clusters.

|Item|Dedicated Kubernetes cluster|Managed Kubernetes cluster|Serverless Kubernetes|
|----|----------------------------|--------------------------|---------------------|
|Features|You must create master nodes and worker nodes.|You only need to create worker nodes. ACK creates and manages master nodes.|You do not need to create master nodes or worker nodes.|
|A dedicated Kubernetes cluster allows you to manage the cluster infrastructure in a more fine-grained manner. You must design, maintain, and upgrade the Kubernetes cluster on your own.|A managed Kubernetes cluster is easy-to-use, cost-efficient, and highly available. You do not need to manage master nodes.|A serverless Kubernetes cluster allows you to start applications directly. You do not need to manage nodes.|
|Billing method|You are not billed for cluster management features. However, you must pay for master nodes, worker nodes, and other infrastructure.|-   ACK Standard clusters: You are not billed for cluster management features. However, you must pay for worker nodes and other infrastructure.
-   [ACK Pro clusters](/intl.en-US/User Guide for Kubernetes Clusters/Introduction/ACK Pro clusters.md): You are billed based on the number of clusters or the subscription method.

|You are billed for the resources consumed by pods and the resource usage duration. The duration is measured in seconds.|
|Scenarios|Applies to all scenarios.|Applies to all scenarios.|Applies to batch tasks, urgent application scale-out, and continuous integration \(CI\) or continuous delivery \(CD\).|
|User profile|![Dedicated Kubernetes cluster](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3952183061/p143465.png)

|![2](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3952183061/p143466.png)

|![3](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3952183061/p143467.png) |
|Cluster creation procedure|![Dedicated Kubernetes cluster](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3952183061/p143471.png)

|![Managed Kubernetes cluster](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3952183061/p143472.png)

|![ASK](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3952183061/p143473.png) |

## Features

-   Cluster management
    -   Create a cluster: You can create multiple types of clusters as needed, choose multiple types of worker nodes, and flexibly custom the configurations.
    -   Upgrade a cluster: You can upgrade Kubernetes with a few clicks and manage the upgrade of system components in a unified manner.
    -   Manage node pools: You can manage the lifecycle of node pools. You can configure node pools of different specifications in a cluster, such as VSwitches, runtimes, operating systems, and security groups.
    -   [Elastic scaling](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Overview.md): You can scale in and scale out resources in the console with a few clicks based on your needs. You can also use service-level affinity rules and scale up resources.
    -   [Manage multiple clusters](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Overview.md): You can manage applications in data centers and clusters in multiple clouds and regions in a unified manner.
    -   Manage permissions: You can grant permissions to users in the RAM console or by using role-based access control \(RBAC\) policies.
-   Application management
    -   Application creation: You can create multiple types of applications based on images and templates. You can configure environment variables, application health checks, data disks, and logging.
    -   Application lifecycle management: You can view, update, and delete applications, roll back application versions, view application events, perform rolling updates of applications, use new application versions to replace old application versions, and use triggers to redeploy applications.
    -   Application pod scheduling: You can schedule application pods based on the following three policies: pod affinity, node affinity, and pod anti-affinity.
    -   Application pod scaling: You can scale application pods manually or by using the Horizontal Pod Autoscaler \(HPA\).
    -   Application release: Phased release and blue-green release are supported.
    -   App Catalog: You can use App Catalog to simplify the integration of Alibaba Cloud services.
    -   [Application center](): After an application is deployed, the application center displays the topology of the application on one page. You can also manage and roll back the application version in scenarios such as continuous deployment.
-   Storage and network
    -   [Storage plug-ins](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Volume plug-ins.md): FlexVolume and CSI are supported.
    -   Volumes and persistent volume claims \(PVCs\):
        -   You can create Block Storage volumes, Apsara File Storage NAS volumes, Object Storage Service \(OSS\) volumes, and Cloud Paralleled File System \(CPFS\) volumes.
        -   You can bind a volume to a PVC.
        -   You can dynamically create and migrate volumes.
        -   You can view and update volumes and PVCs by running scripts.
    -   [Network](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Overview.md):
        -   You can create clusters in VPCs and use the Flannel and Terway network plug-ins.
        -   You can specify CIDR blocks of services and pods.
        -   You can use the NetworkPolicy feature.
        -   You can use ingresses to route requests from outside a cluster to services within the cluster.
-   O&M and security
    -   Monitoring: You can monitor clusters, nodes, applications, and pods. You can use the Prometheus plug-in.
    -   Logging: You can view cluster logs, pod logs, and application logs.
    -   The [Runtime Security](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Security/Use Runtime Security to monitor ACK clusters and configure alerts.md) page allows you to manage security policies of the container runtime, configure routine inspection of application security, and configure security monitoring and alerting on the runtime. This improves the overall security capabilities of containers.
    -   [Sandboxed-Container](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Sandboxed-Container overview.md) allows you to run an application in a sandboxed and lightweight virtual machine. This virtual machine has a dedicated kernel, isolates applications from each other, and provides enhanced security. Sandboxed-Container is suitable in scenarios such as untrusted application isolation, fault isolation, performance isolation, and load isolation among multiple users.
    -   [TEE-based confidential computing](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/TEE-based confidential computing.md) is a cloud-native and all-in-one solution based on Intel Software Guard Extensions \(SGX\). This solution ensures security, integrity, and confidentiality of data in use. It also lowers the costs of developing, delivering, and managing trusted applications and confidential computing applications. Confidential computing allows you to isolate sensitive data and code by using a trusted execution environment.

## Product architecture

![architecture](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1307783061/p177335.png)

-   [Alibaba Cloud Container Registry \(ACR\)]()provides managed security services and lifecycle management of cloud-native assets. ACR distributes images to clusters in different scenarios and is seamlessly integrated with ACK to provide an all-in-one solution for cloud-native application management.
-   [Alibaba Cloud Service Mesh \(ASM\)]() is a managed service mesh platform that allows you to manage the traffic of an application that uses the microservices architecture in a unified manner. ASM is compatible with the open source Istio service mesh platform and allows you to manage the traffic of multiple Kubernetes clusters. ASM provides a unified way to manage the communications among containerized applications and applications on virtual machines.
-   [Alibaba Cloud Serverless Kubernetes \(ASK\)](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Overview.md) provides serverless Kubernetes clusters based on elastic computing. You can create containerized applications without managing or maintaining clusters.
-   [Alibaba Cloud Genomics Service \(AGS\)](/intl.en-US/User Guide for Genomics Service/Overview.md) is a genome sequencing and secondary analysis service based on big data. It serves biotechnology industry users. AGS is an efficient, elastic, and reliable service that requires low costs.
-   [Alibaba Cloud Container Service for Kubernetes@Edge \(ACK@Edge\)](/intl.en-US/User Guide for Edge Container Service/Managed edge cluster overview.md) is a Kubernetes cluster based on the standard Kubernetes runtime environment. It integrates the cloud, edge, and terminals to deliver, maintain, and manage applications. ACK@Edge also enhances node autonomy in edge clusters.

## Use ACK

Click the button below to use ACK.

