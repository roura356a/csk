---
keyword: [ACK, Kubernetes, quick start, FAQ]
---

# Quick start

This topic describes how to get started with Container Service for Kubernetes \(ACK\) in steps and provides answers to some frequently asked questions about ACK.

## Procedure

The following flowchart shows how to get started with ACK in steps.

![Flowchart for ACK](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8558541061/p7176.png)

1.  Assign roles to your Alibaba Cloud account. For more information, see [ACK default roles](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/ACK default roles.md).

    For more information about how to create a Resource Access Management \(RAM\) permission policy and assign role-based access control \(RBAC\) roles to a RAM user, see [Create a custom RAM policy](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create a custom RAM policy.md) and [Assign RBAC roles to a RAM user](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Assign RBAC roles to a RAM user.md).

2.  Create a standard managed Kubernetes cluster. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

    To create a cluster of another type, see the following topics:

    -   [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md).
    -   [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).
    -   [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Introduction/Create a professional managed Kubernetes cluster.md).
    -   [Create a managed edge Kubernetes cluster](/intl.en-US/User Guide for Edge Container Service/Edge cluster management/Create a managed edge Kubernetes cluster.md).
    -   [Create a managed cluster with GPU-accelerated nodes](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU management/Create heterogeneous computing clusters/Create a managed cluster with GPU-accelerated nodes.md) and [Create a dedicated cluster with GPU-accelerated nodes](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU management/Create heterogeneous computing clusters/Create a dedicated cluster with GPU-accelerated nodes.md).
    -   [Create a managed Kubernetes cluster that runs sandboxed containers](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Create a security sandbox cluster/Create a managed Kubernetes cluster that runs sandboxed containers.md) and [Create a dedicated Kubernetes cluster that runs sandboxed containers](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Create a security sandbox cluster/Create a dedicated Kubernetes cluster that runs sandboxed containers.md).
    -   [Create a managed Kubernetes cluster for confidential computing](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/Create a managed Kubernetes cluster for confidential computing.md).
3.  Deploy an application by using an image or orchestration template.

    For more information, see [Deploy a stateless application from an image](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Deploy a stateless application from an image.md) and [Use an orchestration template to create a Linux application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an orchestration template to create a Linux application.md).

    **Note:** If your application consists of multiple Services created from different images, we recommend that you use a YAML file to deploy the application.

4.  Perform O&M operations on the cluster and the application.

    |Cluster O&M|Application O&M|
    |-----------|---------------|
    |Cluster management    -   [Upgrade a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Upgrade cluster/Upgrade a cluster.md)
    -   [Expand an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Expand an ACK cluster.md)
    -   [Manage system components](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Upgrade cluster/Manage system components.md)
|Application deployment    -   [Deploy a stateful application from an image](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Deploy a stateful application from an image.md)
    -   [Create a Job application by using an image](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Create a Job application by using an image.md)
    -   [Create an application from a private image repository](/intl.en-US/Quick Start/Advanced operations/Create an application from a private image repository.md) |
    |Node maintenance    -   [Add existing ECS instances to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Add existing ECS instances to an ACK cluster.md)
    -   [Mark a node as unschedulable](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Mark a node as unschedulable.md)
    -   [Manage nodes in batches](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Manage nodes in batches.md)
    -   [Attach a data disk to a node](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Attach a data disk to a node.md)
|Application and image updates    -   [Use an application trigger to redeploy an application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an application trigger to redeploy an application.md)
    -   [Use aliyun-acr-credential-helper to pull images without a password](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use aliyun-acr-credential-helper to pull images without a password.md)
    -   [Use kritis-validation-hook to automatically verify the signatures of container images](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use kritis-validation-hook to automatically verify the signatures of container images.md) |
    |Node pool management    -   [Create a node pool](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Create a node pool.md)
    -   [Scale out a node pool](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Scale out a node pool.md)
    -   [Schedule an application pod to a specified node pool](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Schedule an application pod to a specified node pool.md)
|Application scaling    -   [Scale an application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Scale an application.md)
    -   [HPA](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/HPA.md)
    -   [Vertical pod autoscaling](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Vertical pod autoscaling.md) |
    |    -   Monitoring management

[Monitor application performance](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/Monitor application performance.md), [Event monitoring](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/Event monitoring.md), [Enable ARMS Prometheus](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/Enable ARMS Prometheus.md), and [Use Prometheus to monitor a Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/Use Prometheus to monitor a Kubernetes cluster.md)

    -   Log management

[Audit logs](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Security/Use the cluster auditing feature.md), [Use Log Service to collect container logs](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Use Log Service to collect container logs.md), [Use Log-pilot to collect ACK cluster logs](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Use Log-pilot to collect ACK cluster logs.md), and [Configure Log4jAppender for Kubernetes and Log Service](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Configure Log4jAppender for Kubernetes and Log Service.md) |


## FAQ

-   **How do I create a Docker image for an application that runs on an ACK cluster?**

    Container Registry allows you to create a container image within a few clicks. For more information about how to create a Docker image for an application, see [Build an image for a Java application by using a Dockerfile with multi-stage builds](). You can also use the open source tool [Derrick](https://github.com/alibaba/derrick) to simplify how to containerize your application.

-   **If I cannot create an image, how do I deploy an application to an ACK cluster?**

    ACK allows you to create applications by using images of the following types: images stored in Container Registry, official images, favorite images, and public images. For more information, see [Deploy a stateless application from an image](/intl.en-US/Quick Start/Basic operations/Deploy a stateless application from an image.md).

-   **How do I plan the network before I create a cluster?**

    Before you create a cluster, make sure that the CIDR block of the virtual private cloud \(VPC\) where you want to deploy the cluster, CIDR blocks of Services, and CIDR blocks of pods do not overlap. You can select an automatically created VPC. In this case, use the default IP addresses when you create a cluster. However, in some complex scenarios, you must plan the IP address of each Elastic Compute Service \(ECS\) instance, pod CIDR blocks, and Service CIDR blocks. For more information, see [Plan CIDR blocks for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Plan CIDR blocks for an ACK cluster.md).

-   **Which network plug-in should I choose when I create a cluster, Terway or Flannel?**

    Flannel is a simple and stable container network interface \(CNI\) plug-in developed by the Kubernetes community. However, Flannel provides only basic features and does not support standard Kubernetes network policies. Terway is a network plug-in developed by Alibaba Cloud. Terway supports standard Kubernetes network policies and bandwidth throttling on containers. Terway outperforms Flannel in terms of network performance. For more information, see [Work with Terway](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Container network/Work with Terway.md).

-   **What can I do if I failed to create a cluster?**

    You can view the cluster logs for troubleshooting. For more information, see [Failed to create a Kubernetes cluster](https://www.alibabacloud.com/help/doc-detail/148453.htm).

-   **How do I access Kubernetes workloads over the Internet?**

    ACK allows you to use the following methods to access workloads over the Internet:

    -   Access a node port.
    -   [Use a Server Load Balancer \(SLB\) instance](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Use an existing SLB instance to expose an application.md).
    -   [Nginx Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Create an Ingress.md)
    -   [Use a Domain Name System \(DNS\) server](https://developer.aliyun.com/article/633412).
    -   Use a NAT gateway. The NAT gateway must have Destination Network Address Translation \(DNAT\) entries configured.
-   **If multiple workloads exist in a cluster, how can a workload be accessed by other workloads in the cluster?**

    To access a workload from other workloads in the same cluster, use the internal domain name or a ClusterIP Service.

    Assume that Workload A and Workload B exist in the same cluster. To allow Workload A to access Workload B, create a ClusterIP Service for Workload B. For more information, see [Manage Services](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Manage Services.md). After you create a ClusterIP Service, Workload A can access Workload B by using the following connect strings:

    -   `<The name of the ClusterIP Service>. <The namespace to which Workload B belongs>.svc.cluster.local:<Port number>`
    -   `ClusterIP:<Port number>`
-   **What are the considerations when I expose Services through SLB instances?**

    If you create a LoadBalancer Service, cloud controller manager \(CCM\) automatically creates and configures an SLB instance for the Service. We recommend that you do not configure the SLB instance in the SLB console. Otherwise, the Service may be unavailable. For more information, see [Considerations for configuring a LoadBalancer type Service](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Considerations for configuring a LoadBalancer type Service.md).

-   **How do I pull private images from Container Registry?**

    We recommend that you use the aliyun-acr-credential-helper component. By default, each cluster has aliyun-acr-credential-helper installed. You can use this component to pull private images from Container Registry without a password. For more information, see [Use aliyun-acr-credential-helper to pull images without a password](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use aliyun-acr-credential-helper to pull images without a password.md).


