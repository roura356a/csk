---
keyword: [ACK, Kubernetes, flowchart, FAQ]
---

# Flowchart

This topic provides the flowchart and some commonly asked questions \(FAQ\) when you use Container Service for Kubernetes \(ACK\).

## Procedure

The flowchart consists of the following steps:

![Flowchart for ACK](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8558541061/p7176.png)

1.  Assign roles to your Alibaba Cloud account. For more information, see [Default roles](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Role authorization.md).

    For more information about RAM policies and role-based access control \(RBAC\), see [Create a custom RAM policy](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create custom RAM permission policies.md) and [Assign RBAC roles to a RAM user](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Assign RBAC roles to a RAM user.md).

2.  Create a standard managed cluster. For more information, see [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md).

    To create a cluster of another type, see the following topics:

    -   [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md).
    -   [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).
    -   [Create an ACK Pro cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create an ACK Pro cluster.md).
    -   [Create a managed edge cluster](/intl.en-US/User Guide for Edge Container Service/Edge cluster management/Create a managed edge cluster.md).
    -   [Create a managed GPU cluster](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU management/Create heterogeneous computing clusters/Create a managed GPU cluster.md) and [Create a dedicated GPU cluster for heterogeneous computing](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU management/Create heterogeneous computing clusters/Create a dedicated GPU cluster for heterogeneous computing.md).
    -   [Create a managed Kubernetes cluster that runs sandboxed containers](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Create a managed Kubernetes cluster that runs sandboxed containers.md) and [Create a dedicated Kubernetes cluster that runs sandboxed containers](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Create a dedicated Kubernetes cluster that runs sandboxed containers.md).
    -   [Create a managed Kubernetes cluster that supports confidential computing](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/Create a managed Kubernetes cluster that supports confidential computing.md).
3.  Deploy an application by using an image or orchestration template.

    For more information, see [Use an image to create a stateless application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an image to create a stateless application.md) and [Use an orchestration template to create a Linux application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an orchestration template to create a Linux application.md).

    **Note:** If your application consists of multiple services created from different images, we recommend that you use a YAML file to deploy the application.

4.  Perform O&M operations on the cluster and the application.

    |Cluster O&M|Application O&M|
    |-----------|---------------|
    |Cluster management    -   [Upgrade a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Upgrade cluster/Upgrade a cluster.md)
    -   [Expand a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Expand a cluster.md)
    -   [Manage system components](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Upgrade cluster/Manage system components.md)
|Application deployment    -   [Deploy stateful applications from images](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an image to create a stateful application.md)
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
    |    -   Monitoring

[Monitor application performance](/intl.en-US/User Guide for Kubernetes Clusters/Monitoring management/Monitor application performance.md), [Monitor application architecture](), [Event monitoring](/intl.en-US/User Guide for Kubernetes Clusters/Monitoring management/Monitor events.md), [Enable ARMS Prometheus](/intl.en-US/User Guide for Kubernetes Clusters/Monitoring management/Enable ARMS Prometheus.md), and [Deploy Prometheus to a Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Monitoring management/Deploy Prometheus to a Kubernetes cluster.md)

    -   Log management

[Audit logs](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Security/Use kube-apiserver to collect audit logs.md), [Use Log Service to collect container logs](/intl.en-US/User Guide for Kubernetes Clusters/Log management/Use Log Service to collect container logs.md), [Use log-pilot to collect logs of a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Log management/Use log-pilot to collect logs of a cluster.md), and [Configure Log4jAppender for Kubernetes and Log Service](/intl.en-US/User Guide for Kubernetes Clusters/Log management/Configure Log4jAppender for Kubernetes and Log Service.md) |


## FAQ

-   **How do I build a Docker image for an application that runs on an ACK cluster?**

    Container Registry allows you to build a container image within a few clicks. For more information about how to build a Docker image for an application, see [Build an image for a Java application by using a Dockerfile with multi-stage builds](). You can also use the open source tool [Derrick](https://github.com/alibaba/derrick) to dockerize an application in a simplified manner.

-   **If I do not want to build an image, how do I deploy an application to an ACK cluster?**

    ACK allows you to create applications by using images of the following types: images stored in Container Registry, official images, favorite images, and public images. For more information, see [Deploy stateless applications from images](/intl.en-US/Quick Start/Basic operations/Deploy stateless applications from images.md).

-   **How do I plan CIDR blocks before I create a cluster?**

    Before you create a cluster, make sure that the CIDR blocks of virtual private clouds \(VPCs\), services, and pods do not overlap. You can select to create a VPC automatically. In this case, use the default network address when you create a cluster. However, in some complex scenarios, you must plan CIDR blocks for Elastic Compute Service \(ECS\) instances, pods, and services. For more information, see [Assign CIDR blocks to resources in a Kubernetes cluster under a VPC](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Assign CIDR blocks to resources in a Kubernetes cluster under a VPC.md).

-   **How do I select the Terway or Flannel plug-in when I create a cluster?**

    Flannel is a simple and stable container network interface \(CNI\) plug-in developed by the community. However, Flannel only supports simple features and does not support standard Kubernetes network policies. Terway, a network plug-in developed by Alibaba Cloud, supports standard Kubernetes network policies and bandwidth throttling on containers. Terway outperforms Flannel in terms of network performance. For more information, see [Use Terway](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Use Terway.md).

-   **How do I handle a cluster creation failure?**

    You can view the cluster events for troubleshooting. For more information, see [Failed to create a Kubernetes cluster](https://www.alibabacloud.com/help/doc-detail/148453.htm).

-   **How do I access Kubernetes workloads over the Internet?**

    You can use the following methods to access workloads over the Internet:

    -   Use a NodePort
    -   [Use Server Load Balancer \(SLB\) instances](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Access services by using SLB instances.md)
    -   [Nginx Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Create an Ingress on the web UI.md)
    -   [Use an external DNS](https://developer.aliyun.com/article/633412)
    -   Use a Destination Network Address Translation \(DNAT\) gateway
-   **If multiple workloads exist in a cluster, how can a workload be accessed by other workloads in the cluster?**

    To access a workload from other workloads in the same cluster, use the internal DNS or ClusterIP service.

    Assume that Workload A and Workload B exist in a cluster. To allow Workload A to access Workload B, create a service of the ClusterIP type for Workload B. For more information, see [Create a service](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Create a service.md). After you create a ClusterIP service, Workload A can use one of the following methods to access Workload B:

    -   `<ClusterIP service name>. <Namespace to which Workload B belongs>.svc.cluster.local:<Port number>`
    -   `ClusterIP:<Port number>`
-   **What are the considerations when I access services through SLB instances?**

    If you create a service of the LoadBalancer type, Cloud Controller Manager \(CCM\) automatically creates and configures an SLB instance for the service. We recommend that you do not configure the SLB instance in the SLB console. This may cause the unavailability of the service. For more information, see [Considerations for configuring a LoadBalancer service](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Considerations for configuring a LoadBalancer service.md).

-   **How do I pull private images from Container Registry?**

    We recommend that you use the aliyun-acr-credential-helper component. By default, each cluster has aliyun-acr-credential-helper installed. You can use this component to pull images without a password from Container Registry. For more information, see [Use aliyun-acr-credential-helper to pull images without a password](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use aliyun-acr-credential-helper to pull images without a password.md).


