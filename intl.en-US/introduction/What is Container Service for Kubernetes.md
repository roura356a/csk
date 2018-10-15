# What is Container Service for Kubernetes {#concept_cbm_1zc_l2b .concept}

Container Service for Kubernetes provides the high-performance and scalable container application management service, which enables you to manage the lifecycle of enterprise-class containerized applications by using Kubernetes. By simplifying the setup and capability expansion of cluster and integrating with the Alibaba Cloud abilities of virtualization, storage, network, and security, Container Service for Kubernetes makes an ideal running cloud environment for Kubernetes containers. As a service provider certified by Kubernetes and the world's first platform service that has passed Kubernetes consistency certification, Container Service provides you with professional support and services.

Container Service for Kubernetes contains the classic dedicated Kubernetes mode and the serverless Kubernetes mode for you to choose on demand.

-   Classic dedicated Kubernetes mode: You can get more fine-grained control over cluster infrastructure and container applications, for example, select the host instance specification and the operating system, specify Kubernetes version, custom Kubernetes attribute switch settings, and more. Alibaba Cloud Container Service for Kubernetes is responsible for creating the underlying cloud resources for the cluster, upgrading and other automated operations for the cluster. You need to plan, maintain, and upgrade the server cluster. You can add servers to or remove servers from the cluster manually or automatically.
-   Serverless Kubernetes mode: You do not need to create the underlying virtualization resource. To launch the application directly, use Kubernetes commands to specify the application container image, CPU and memory requirements as well as external service methods.

## Features {#section_ts5_5jb_n2b .section}

**Cluster management**

-   You can create a classic dedicated Kubernetes cluster through the console within ten minutes. GPU instances and ECS Bare Metal \(EBM\) Instance are supported. You can also create a highly available cluster that is across zones.
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

    Supports the monitoring at the level of containers and virtual machines \(VMs\).  You can also integrate Container Service with third-party open-source monitoring solutions.

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

