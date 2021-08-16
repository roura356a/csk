---
keyword: [Kubernetes cluster, feature, K8s cluster, overview]
---

# ACK cluster overview

Kubernetes is an open source orchestration platform that is commonly used to manage containerized applications and services. Container Service for Kubernetes \(ACK\) combines native Kubernetes with the virtualization, storage, networking, and security capabilities of Alibaba Cloud, and provides secure, high-performance, and scalable ACK clusters. ACK makes it easy to manage containerized applications with Kubernetes on Alibaba Cloud and simplifies the process of creating and scaling clusters. This allows you to focus on the development and management of containerized applications. This topic describes the features, types, and limits of ACK clusters.

## Background information

Alibaba Cloud provides different types of ACK clusters to meet the requirements of diverse scenarios.

-   ACK clusters are most commonly used and are suitable for most scenarios.
-   Serverless Kubernetes \(ASK\) clusters are suitable for agile workloads that require quick scaling, and scenarios in which individual tasks or multiple parallel tasks need to be processed. For more information, see [ASK overview](/intl.en-US/User Guide for Serverless Kubernetes Clusters/ASK overview.md).
-   Edge Kubernetes clusters are the most suitable option when you want to handle edge computing services such as Internet of Things \(IoT\) and Content Delivery Network \(CDN\). For more information, see [ACK@Edge overview](/intl.en-US/User Guide for Edge Container Service/ACK@Edge overview.md).

ACK also provides highly integrated solutions for fields such as genomics computing and AI-empowered big data computing. ACK optimizes container performance based on the high-performance computing and networking capabilities of Infrastructure-as-a-Service \(IaaS\). ACK allows you to centrally manage clusters that are deployed in multi-cloud or hybrid cloud environments. You can log on to the ACK console to manage your Kubernetes clusters deployed in data centers or third-party clouds.

## Limits

For more information about the limits of ACK clusters, see [Limits](/intl.en-US/Product Introduction/Limits.md).

## Cluster lifecycle

The following table describes the different states of a cluster and the figure shows the transitions between the states.

|State|Description|
|-----|-----------|
|Initializing|Creating the cloud resources that are used by the cluster.|
|Creation Failed|Failed to create the cloud resources that are used by the cluster.|
|Running|The cloud resources used by the cluster are created.|
|Updating|Updating the metadata of the cluster.|
|Scaling|Adding nodes to the cluster.|
|Removing|Removing nodes from the cluster.|
|Upgrading|Upgrading the cluster.|
|Deleting|Deleting the cluster.|
|Deletion Failed|Failed to delete the cluster.|
|Deleted \(invisible to users\)|The cluster is deleted.|

![State transitions](../images/p4752.png "State transitions")

## Cluster type

ACK clusters are classified into professional, standard, and dedicated clusters.

|Item|Professional Kubernetes cluster|Standard Kubernetes cluster|Dedicated Kubernetes cluster|
|----|-------------------------------|---------------------------|----------------------------|
|Features|You need only to create worker nodes. ACK creates and manages master nodes.|You must create master nodes and worker nodes.|
|Managed Kubernetes clusters are easy to use, cost-effective, and highly available. You do not need to manage master nodes. For more information about the differences between standard and professional Kubernetes clusters, see [Comparison](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Introduction to professional managed Kubernetes clusters.md).

|Managed Kubernetes clusters are easy to use, cost-effective, and highly available. You do not need to manage master nodes.|Dedicated Kubernetes clusters allow you to manage the cluster infrastructure in a finer-grained manner. You must design, maintain, and upgrade the clusters on your own.|
|Billing methods|You are charged for cluster management based on the number of clusters. For more information, see [Billing](/intl.en-US/.md).|Cluster management is free of charge. However, you are charged for the use of worker nodes and other infrastructures. For more information, see [Billing](/intl.en-US/.md).|Cluster management is free of charge. However, you are charged for the use of master nodes, worker nodes, and other infrastructures. For more information, see [Billing](/intl.en-US/.md).|
|Scenarios|Applicable to all scenarios.|Applicable to all scenarios.|Applicable to all scenarios.|
|User personas|![2](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/3952183061/p143466.png)

|![Dedicated Kubernetes cluster](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/3952183061/p143465.png) |
|Cluster creation procedure|![Managed Kubernetes cluster](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/3952183061/p143472.png)

|![Dedicated Kubernetes cluster](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/3952183061/p143471.png) |

## Features

The following table describes the features of ACK clusters.

|Feature|Description|
|-------|-----------|
|Cluster management|-   Create clusters: You can create multiple types of cluster based on your requirements, choose multiple types of worker node, and flexibly customize the configurations.
-   Upgrade clusters: You can upgrade Kubernetes with a few clicks and manage the upgrade of system components in a unified manner.
-   Manage node pools: You can manage the lifecycle of node pools. You can configure node pools of different specifications in a cluster, such as vSwitches, runtimes, operating systems, and security groups.
-   [Elastic scaling](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Auto scaling overview.md): You can scale in and scale out resources in the console with a few clicks based on your requirements. You can also use service-level affinity rules and scale up resources.
-   [Manage multiple clusters](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Overview of the multi-cloud and hybrid cloud solution.md): You can manage applications in data centers and clusters in multiple clouds and regions in a unified manner.
-   Manage permissions: You can grant permissions to users in the RAM console or by using role-based access control \(RBAC\) policies. |
|Node pool| |
|Application management|-   Create applications: You can create multiple types of application based on images and templates. You can configure environment variables, application health checks, data disks, and logging.
-   Manage lifecycles: You can view, update, and delete applications, roll back application versions, view application events, perform rolling updates of applications, use new application versions to replace old application versions, and use triggers to redeploy applications.
-   Schedule applications: You can schedule application pods based on the following three policies: pod affinity, node affinity, and pod anti-affinity.
-   Scale applications: You can scale the number of application pods manually or by using the Horizontal Pod Autoscaler \(HPA\).
-   Release applications: Phased release and blue-green release are supported.
-   App Catalog: You can use App Catalog to simplify the integration of Alibaba Cloud services.
-   [Application center](/intl.en-US/User Guide for Kubernetes Clusters/Application center/Overview.md): After an application is deployed, the application center displays the topology of the application on one page. You can also manage and roll back the application version in scenarios such as continuous deployment.
-   Back up and restore applications: You can back up Kubernetes applications and restore applications from backup data. For more information, see [Back up and restore applications](/intl.en-US/User Guide for Kubernetes Clusters/Disaster recovery center/Back up and restore applications.md). |
|Storage|-   [Storage plug-ins](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Storage overview.md): FlexVolume and CSI are supported.
-   Volumes and persistent volume claims \(PVCs\):
    -   You can create Block Storage volumes, Apsara File Storage NAS \(NAS\) volumes, Object Storage Service \(OSS\) volumes, and Cloud Paralleled File System \(CPFS\) volumes.
    -   You can bind a volume to a PVC.
    -   You can dynamically create and migrate volumes.
    -   You can view and update volumes and PVCs by running scripts.
-   [Network](/intl.en-US/User Guide for Kubernetes Clusters/Network/Overview.md):
    -   You can create clusters in virtual private clouds \(VPCs\) and use the Flannel or Terway network plug-ins.
    -   You can specify CIDR blocks of Services and pods.
    -   You can use the NetworkPolicy feature.
    -   You can use Ingresses to route requests from outside a cluster to services within the cluster. |
|Networking| |
|O&M and security|-   Monitoring: You can monitor clusters, nodes, applications, and pods. You can use the Prometheus plug-in.
-   Logging: You can view cluster logs, pod logs, and application logs.
-   The [Runtime Security](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Security/Use the runtime security feature to monitor ACK clusters and configure alerts.md) page allows you to manage security policies of the container runtime, configure routine inspections of application security, and configure security monitoring and alerting on the runtime. This enhances the overall security capabilities of containers.
-   [Sandboxed-Container](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Sandboxed-Container overview.md) allows you to run an application in a sandboxed and lightweight virtual machine. This virtual machine has a dedicated kernel, isolates applications from each other, and provides enhanced security. Sandboxed-Container is suitable in scenarios such as untrusted application isolation, fault isolation, performance isolation, and load isolation among multiple users.
-   [TEE-based confidential computing](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/TEE-based confidential computing.md) is a cloud-native and all-in-one solution based on Intel Software Guard Extensions \(SGX\). This solution ensures data security, integrity, and confidentiality. Confidential computing allows you to isolate sensitive data and code by using a trusted execution environment. |
|Heterogeneous computing|-   GPU and NPU computing: allows you to create clusters that use GPU-accelerated or NPU-accelerated instances as worker nodes. Supports scheduling, monitoring, auto scaling, and O&M management of GPU resources. For more information, see [Create a managed Kubernetes cluster with GPU-accelerated nodes](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/Create heterogeneous computing clusters/Create a managed Kubernetes cluster with GPU-accelerated nodes.md).
-   GPU sharing: allows you to implement a GPU sharing framework in your cluster deployed in the cloud or in a data center to run multiple containers on a GPU. For more information, see [cGPU overview](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Shared GPU scheduling/cGPU overview.md).
-   Cloud-native AI: provides cloud-native AI computing capabilities and supports orchestration and management of data computing tasks. For more information, see [Overview](/intl.en-US/Cloud-native AI User Guide/Overview.md). |
|Developer services|-   API: provides multiple API operations and supports the Kubernetes-native API. For more information, see [Use the Kubernetes API](/intl.en-US/API Reference/Use the Kubernetes API.md) and [API overview](/intl.en-US/API Reference/API overview.md).
-   CLI: supports Cloud Shell and the open source kubectl tool. For more information, see [Use kubectl on Cloud Shell to manage ACK clusters](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl on Cloud Shell to manage ACK clusters.md) and [Connect to ACK clusters by using kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md). |

## Features available only to users in a whitelist

The following table describes the features that are available only to users in a whitelist.

|Feature|Description|Professional Kubernetes cluster|Standard Kubernetes cluster|Dedicated Kubernetes cluster|
|-------|-----------|-------------------------------|---------------------------|----------------------------|
|GPU-accelerated node equipped with the 2080 Ti graphics card|Allows you to use Elastic Compute Service \(ECS\) nodes of the gn6t instance family in the cluster. **Note:** ECS nodes of the gn6t instance family are available only to users that are in a whitelist. To use this feature, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

|![勾](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/7998975261/p278633.png)

|![勾](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/7998975261/p278633.png)

|![勾](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/7998975261/p278633.png) |
|Genomics computing|Provides a cluster template which helps you create professional Kubernetes clusters that are suitable for genomics computing.|![勾](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/7998975261/p278633.png)

| | |
|Low-specification instance type|Allows you to select nodes that have 2 CPU cores when you create clusters.|![勾](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/7998975261/p278633.png)

|![勾](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/7998975261/p278633.png)

|![勾](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/7998975261/p278633.png) |
|Custom images|Allows you to select custom images when you create clusters or node pools.|![勾](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/7998975261/p278633.png)

|![勾](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/7998975261/p278633.png)

|![勾](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/7998975261/p278633.png) |
|Existing security groups|Allows you to select existing security groups when you create clusters or node pools.|![勾](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/7998975261/p278633.png)

|![勾](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/7998975261/p278633.png)

|![勾](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/7998975261/p278633.png) |
|Exclusive ENI mode|Allows you to select the exclusive Elastic Network Interface \(ENI\) mode for the Terway plug-in when you create clusters. The Terway plug-in in exclusive ENI mode can help improve network performance.|![勾](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/7998975261/p278633.png)

|![勾](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/7998975261/p278633.png)

|![勾](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/7998975261/p278633.png) |

## Open source projects

For more information about the open source projects that are used by ACK, see [t2010588.dita\#concept\_2010588](/intl.en-US/Product Introduction/Open source projects.md).

## FAQ

|-   [FAQ about authorization management](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/FAQ about authorization management.md)
-   [FAQ about cluster management](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/FAQ about cluster management.md)
-   [FAQ about node management](/intl.en-US/User Guide for Kubernetes Clusters/Node management/FAQ about node management.md)
-   [FAQ about container networks](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/FAQ about container networks.md)
-   [FAQ about Services](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/FAQ about Services.md)
-   [FAQ about Ingresses](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/FAQ about Ingresses.md)
-   [FAQ about multi-cloud and hybrid cloud](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/FAQ about multi-cloud and hybrid cloud.md)

|-   [FAQ about application management](/intl.en-US/User Guide for Kubernetes Clusters/Application management/FAQ about application management.md)
-   [FAQ about container security](/intl.en-US/User Guide for Kubernetes Clusters/Security management/FAQ about container security.md)
-   [FAQ about CSI](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/FAQ about CSI.md)
-   [FAQ about FlexVolume](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/FAQ about FlexVolume.md)
-   [GPU/NPU FAQ](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU/NPU FAQ.md)
-   [FAQ about auto scaling](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/FAQ about HPA.md) |

