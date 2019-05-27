# Alibaba Cloud Container Service for Kubernetes Overview {#concept_cbm_1zc_l2b .concept}

Alibaba Cloud Container Service for Kubernetes provides the high-performance and scalable container application management service, which enables you to manage the lifecycle of enterprise-class containerized applications by using Kubernetes. By simplifying the setup and capability expansion of cluster and integrating with the Alibaba Cloud abilities of virtualization, storage, network, and security, Container Service for Kubernetes makes an ideal running cloud environment for Kubernetes containers. As a service provider certified by Kubernetes and the world's first platform service that has passed Kubernetes consistency certification, Container Service provides you with professional support and services related to containers.

Alibaba Cloud Container Service for Kubernetes provides you with three types of Kubernetes clusters: dedicated Kubernetes clusters, managed Kubernetes clusters, and serverless Kubernetes clusters.

-   Dedicated Kubernetes cluster: You must create three Master nodes and one or multiple Worker nodes for the cluster. In addition, you need to plan, maintain, and upgrade the cluster as needed. With such a Kubernetes cluster, you can control cluster infrastructures in a more fine-grained manner.
-   Managed Kubernetes cluster: You only need to create Worker nodes for the cluster, and Alibaba Cloud Container Service for Kubernetes creates and manages Master nodes for the cluster. This type of Kubernetes cluster is easy to use, low-cost, and highly available. You can focus on the services supported by the cluster without needing to operate and maintain the Kubernetes cluster Master nodes.
-   Serverless Kubernetes cluster: You do not need to create or manage any Master nodes or Worker nodes for the cluster. You can directly use the Container Service console or the command line interface to set container resources, specify container images for applications, set methods to provide services, and start applications.

## Features {#section_ts5_5jb_n2b .section}

**Cluster management**

-   You can create a dedicated Kubernetes cluster, a managed Kubernetes cluster, or a serverless Kubernetes cluster win one click in the Container Service console. GPU instances and ECS Bare Metal \(EBM\) instance can be created for a Kubernetes cluster. You can also create a highly available Kubernetes cluster for which multiple zones are set.
-   Provides OS images for optimizing containers, and the Kubernetes and Docker versions of **stability testing and security hardening**.
-   Supports multi-cluster management, highly available clusters across zones, and cluster federation management.

**One-stop container lifecycle management**

-   **Network** 

    Provides the high performance Virtual Private Cloud \(VPC\) and elastic network interfaces \(ENI\) network plug-in optimized for Alibaba Cloud, which is 20% better than the average network solution.

    Supports container access policies and flow control restrictions.

-   **Storage** 

    Container Service integrates with Alibaba Cloud cloud disk, Network Attached Storage \(NAS\), and Object Storage Service \(OSS\), and provides the standard FlexVolume drive.

    Supports dynamic creation and migration of storage volumes.

-   **Logs** 

    Supports high-performance automatic log collection and integrating with Alibaba Cloud Log Service.

    You can also integrate Container Service with third-party open-source log solutions.

-   **Monitoring** 

    Supports the monitoring at the level of containers and virtual machines \(VMs\). You can also integrate Container Service with third-party open-source monitoring solutions.

-   **Permission** 

    Supports Resource Access Management \(RAM\) authorization and management at the level of clusters.

    Supports permission configuration management at the level of applications.

-   **Application management** 

    Support gray release and blue-green release.

    Support application monitoring and application elastic scaling.

    Supports one-click deployment of the Helm application by the built-in application store. Supports the directory of services to simplify cloud service integration.


**Easily deal with upstream and downstream delivery process by using high-availability scheduling policy**

-   Supports affinity policy and horizontal scaling of services.
-   Supports high availability across zones and disaster recovery.
-   Supports the APIs for cluster and application management to easily interconnect with the continuous integration and private deployment system.

