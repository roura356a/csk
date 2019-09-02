# What is ACK? {#concept_cbm_1zc_l2b .concept}

Container Service for Kubernetes \(ACK\) is a high-performance and scalable containerized application management service, which enables you to manage the entire lifecycle of enterprise-level containerized applications. ACK simplifies the deployment and scale-out operations of Kubernetes clusters and integrates Alibaba Cloud capabilities of virtualization, storage, networking, and security. Based on these capabilities, ACK provides an ideal runtime environment for Kubernetes containerized applications. Container Service is one of the first certified service platforms that obtain the certificate of conformance from Kubernetes in the world. It provides professional support and services related to containers.

ACK provides the following three types of Kubernetes clusters: Dedicated Kubernetes clusters, Managed Kubernetes clusters, and Serverless Kubernetes clusters.

-   Dedicated Kubernetes clusters: You must create three master nodes and several worker nodes for a Dedicated Kubernetes cluster to achieve high availability. This type of Kubernetes cluster allows you to manage the cluster infrastructure in a more fine-grained manner. It requires you to plan, maintain, and upgrade the Kubernetes clusters on your own.
-   Managed Kubernetes: You only need to create worker nodes. The master nodes are created and managed by Alibaba Cloud Managed Kubernetes. This type of Kubernetes cluster is easy to use with low cost and high availability. You can focus on the business without the need to operate and maintain the Kubernetes cluster master nodes.
-   Serverless Kubernetes: You do not need to create and manage any master nodes or worker nodes. You can use the Container Service console or command line interface to configure resources for containers, specify container images for applications, provide methods for external access, and start applications.

## Features {#section_ts5_5jb_n2b .section}

-   **Cluster management** 
    -   You can create a Dedicated Kubernetes cluster, Managed Kubernetes cluster, and Serverless Kubernetes cluster in an easy way through the Container Service console. GPU instances and ECS Bare Metal \(EMB\) instances can be created in the Kubernetes cluster. You can also create clusters across multiple zones to achieve high availability.
    -   ACK provides optimized OS images of Kubernetes containers and offers Kubernetes clusters and Docker versions with **high stability and reinforced security**.
    -   ACK supports multi-cluster management, high-availability clusters across zones, and cluster federation management.
-   **All-in-one container management** 
    -   **Network** 

        Provides high-performance VPC and elastic network interface \(ENI\) plug-ins. The performance of these plug-ins is 20% better than the performance of regular network solutions.

        Supports container access policies and traffic controls.

    -   **Storage** 

        Supports Alibaba Cloud disks, Apsara File Storage NAS, and Object Storage Service \(OSS\) buckets. FlexVolume drivers are also provided.

        Supports dynamic creation and migration of storage volumes.

    -   **Logs** 

        Supports log collection and integration into Log Service.

        Supports the integration with third-party open source logging solutions.

    -   **Monitoring** 

        Supports monitoring containers at both the container level and virtual machine \(VM\) level. You can also integrate Container Service with third-party open source monitoring solutions.

    -   **Access control** 

        Supports Resource Access Management \(RAM\) for authorization and management at the level of clusters.

        Supports permission configuration and management at the level of applications.

    -   **Applications** 

        Supports canary release and blue-green release.

        Supports application monitoring and scaling.

        Supports the built-in application store and deploys applications in an easy way by using Helm.

        Supports Service Catalog to simplify cloud service integration.

-   **High-availability scheduling policies and simple delivery processes** 
    -   Supports affinity policies and horizontal scaling of services.
    -   Provides high availability and disaster recovery across zones.
    -   Provides API operations for cluster and application management to easily implement continuous integration and private system deployment.

