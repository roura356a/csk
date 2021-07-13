---
keyword: [ACK, K8s, Container Service, Container Service for Kubernetes]
---

# What is Container Service for Kubernetes?

Container Service for Kubernetes \(ACK\) is one of the service platforms in the world that have first passed the certification of the Certified Kubernetes Conformance Program. ACK provides high-performance management of containerized applications and allows you to manage enterprise-class containerized applications throughout the application lifecycle. This provides an efficient way for you to run containerized applications in the cloud.

## Cluster types

ACK provides the following three types of Kubernetes clusters: dedicated Kubernetes clusters, managed Kubernetes clusters, and serverless Kubernetes \(ASK\) clusters.

|Item|Dedicated Kubernetes cluster|Managed Kubernetes cluster|Serverless Kubernetes|
|----|----------------------------|--------------------------|---------------------|
|Features|You must create master nodes and worker nodes.|You only need to create worker nodes. ACK creates and manages master nodes.|You do not need to create master nodes or worker nodes.|
|A dedicated Kubernetes cluster allows you to manage the cluster infrastructure in a more fine-grained manner. You must design, maintain, and upgrade the Kubernetes cluster on your own.|A managed Kubernetes cluster is easy to use, cost-efficient, and highly available. You do not need to manage master nodes.|An ASK cluster allows you to directly start applications. You do not need to manage nodes.|
|Billing methods|You are not charged for cluster management. However, you must pay for the use of master nodes, worker nodes, and other infrastructures.|-   Standard managed Kubernetes clusters: You are not charged for cluster management. However, you must pay for the use of worker nodes and other infrastructures.
-   [Professional managed Kubernetes clusters](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Introduction to professional managed Kubernetes clusters.md): You are charged based on the number of clusters or the subscription billing method.

|You are charged for the amount of resources consumed by pods and the resource usage duration. The duration is measured in seconds.|
|Scenarios|Applies to all scenarios.|Applies to all scenarios.|Applies to batch tasks, urgent application scale-out, and continuous integration \(CI\)/continuous delivery \(CD\) tests.|
|User personas|![Dedicated Kubernetes cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3952183061/p143465.png)

|![2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3952183061/p143466.png)

|![3](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3952183061/p143467.png) |
|Cluster creation procedure|![Dedicated Kubernetes cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3952183061/p143471.png)

|![Managed Kubernetes cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3952183061/p143472.png)

|![ASK](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3952183061/p143473.png) |

## Features

-   Cluster management
    -   Cluster creation: You can create multiple types of clusters as needed, choose multiple types of worker nodes, and customize cluster configurations.
    -   Cluster upgrade: You can upgrade Kubernetes versions with a few clicks and upgrade system components in a unified manner.
    -   Node pool management: You can manage the lifecycle of node pools. You can configure different specifications for node pools in a cluster, such as vSwitches, runtimes, operating systems, and security groups.
    -   [Elastic scaling](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Overview.md): You can scale in and scale out resources based on your needs with a few clicks in the console. You can also use service-level affinities and horizontal extensions.
    -   [Multi-cluster management](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Overview of the multi-cloud and hybrid cloud solution.md): You can manage applications in data centers and clusters in multiple clouds and regions in a unified manner.
    -   Permission management: You can grant permissions in the Resource Access Management \(RAM\) console or by using role-based access control \(RBAC\) policies.
-   Application management
    -   Application creation: You can create multiple types of applications by using images and templates. You can configure environment variables, application health checks, data disks, and logging.
    -   Application lifecycle management: You can view, update, and delete applications, roll back application versions, view application events, perform rolling updates of applications, use new application versions to replace old application versions, and use triggers to redeploy applications.
    -   Application scheduling: You can schedule pods of applications based on the following policies: pod affinity, node affinity, and pod anti-affinity.
    -   Application scaling: You can scale pods of applications manually or by using the Horizontal Pod Autoscaler \(HPA\).
    -   Application release: Phased releases and blue-green releases are supported.
    -   App Catalog: You can use App Catalog to simplify the integration with Alibaba Cloud services.
    -   [Application center](/intl.en-US/User Guide for Kubernetes Clusters/Application center/Overview.md): After an application is deployed, the application center provides a unified view of the application topology. You can also manage and roll back the application versions in scenarios such as continuous deployment.
    -   Application backup and restoration: This feature allows you to back up Kubernetes applications and restore applications from backup data. For more information, see [Back up and restore applications in Kubernetes clusters]().
-   Storage and network
    -   [Storage plug-ins](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Storage overview.md): FlexVolume and Container Storage Interface \(CSI\) are supported.
    -   Volumes and persistent volume claims \(PVCs\):
        -   You can create Elastic Block Storage \(EBS\) volumes, Apsara File Storage NAS \(NAS\) volumes, Object Storage Service \(OSS\) volumes, and Cloud Paralleled File System \(CPFS\) volumes.
        -   You can bind a volume to a PVC.
        -   You can dynamically create and migrate volumes.
        -   You can view and update volumes and PVCs by running scripts.
    -   [Networks](/intl.en-US/User Guide for Kubernetes Clusters/Network/Overview.md):
        -   You can create clusters in virtual private clouds \(VPCs\) and use the Flannel and Terway network plug-ins.
        -   You can specify CIDR blocks of Services and pods.
        -   You can use the NetworkPolicy feature.
        -   You can use Ingresses to route inbound traffic to Services within the cluster.
-   O&M and security
    -   Monitoring: You can monitor clusters, nodes, applications, and pods. You can use the Prometheus plug-in.
    -   Logging: You can view the log of clusters, pods, and applications.
    -   The [Runtime Security](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Security/Use Runtime Security to monitor ACK clusters and configure alerts.md) page allows you to manage security policies of the container runtime, configure routine inspections of application security, and configure security monitoring and alerting of the container runtime. This improves the overall security capabilities of containers.
    -   [Sandboxed-Container](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Sandboxed-Container overview.md) allows you to run applications in a sandboxed and lightweight VM. This VM has a dedicated kernel, isolates applications from each other, and provides enhanced security. Sandboxed-Container is suitable in scenarios such as untrusted application isolation, fault isolation, performance isolation, and load isolation among multiple users.
    -   [Trusted Execution Environment \(TEE\)-based confidential computing](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/TEE-based confidential computing.md) is a cloud-native and all-in-one solution based on Intel Software Guard Extensions \(SGX\). This solution ensures the security, integrity, and confidentiality of data in use. It also lowers the costs of developing, delivering, and managing trusted applications and confidential computing applications. Confidential computing allows you to isolate sensitive data and code by using a TEE.

## Architecture

The following figure shows the architecture of the ACK portfolio.

![Architecture](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3768516261/p169914.png)

-   [Container Registry]() provides managed security services and lifecycle management of cloud-native assets. Container Registry distributes images to clusters in different scenarios and is seamlessly integrated with ACK to provide an all-in-one solution for cloud-native application management.
-   [Alibaba Cloud Service Mesh \(ASM\)]() is a managed service mesh platform that allows you to manage the traffic of an application that uses the microservices architecture in a unified manner. ASM is compatible with the open source Istio service mesh platform and allows you to manage the traffic of multiple Kubernetes clusters. ASM provides a unified way to manage the communications among containerized applications and applications on VMs.
-   [ASK](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Introduction.md) provides serverless clusters based on the elastic computing infrastructures of Alibaba Cloud. You can create containerized applications without managing or maintaining clusters.
-   [Alibaba Cloud Genomics Service \(AGS\)](/intl.en-US/User Guide for Genomics Service/Overview.md) is a genome sequencing and secondary analysis service based on big data. It serves the biotechnology industry. AGS is an efficient, elastic, and reliable service that requires low costs.
-   [Container Service for Kubernetes@Edge \(ACK@Edge\)](/intl.en-US/User Guide for Edge Container Service/ACK@Edge overview.md) is an edge computing solution based on the standard Kubernetes runtime environment. It integrates the cloud, edge, and terminals to deliver, maintain, and manage applications. ACK@Edge also enhances node autonomy in edge scenarios.

## Use ACK

Click the button below to use ACK.

