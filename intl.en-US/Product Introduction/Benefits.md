# Benefits

This topic describes the benefits of Container Service for Kubernetes \(ACK\) clusters and the disadvantages of self-managed Kubernetes clusters.

## Benefits of ACK

|Benefit|Description|
|-------|-----------|
|Cluster management|-   Provides three cluster types: dedicated Kubernetes cluster, managed Kubernetes cluster, and serverless Kubernetes \(ASK\) cluster.
-   By default, the master nodes of a managed Kubernetes cluster are deployed across three zones to ensure high availability.
-   Allows you to add thousands of Elastic Compute Service \(ECS\) nodes to a single cluster. For more information about resource quotas, see [Quota limits](/intl.en-US/Product Introduction/Limits.md).
-   Allows you to create clusters across zones and register external clusters in the ACK console. For more information about registered clusters, see [Overview of registered clusters](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of external clusters/Overview of registered clusters.md). |
|Elastic resource scaling|-   Automatically scales the number of containers based on container resource usage.
-   Scales out to thousands of nodes within minutes.
-   If your application is deployed on elastic container instances in an ASK cluster, at most 500 pods can be started within 30 seconds.
-   Supports vertical scaling with a few clicks.
-   Supports service-level affinity policies and scale-out.
-   Provides standard, open source Horizontal Pod Autoscaler \(HPA\), Vertical Pod Autoscaler \(VPA\), and Cluster Autoscaler capabilities.
-   Supports scheduled scaling similar to CronHPA and serverless scalability based on virtual-kubelet-autoscaler.
-   Supports fine-grained scheduling of online business based on ack-kubernetes-elastic-workload.
-   Provides alibaba-metrics-adapter to meet different scaling needs. Optimizes application scaling by using approaches such as Ingress gateways and microservice flow control based on Sentinel. |
|All-in-one container management|-   Application management:
    -   Supports phased release, blue-green release, application monitoring, and application autoscaling.
    -   Provides a built-in application marketplace that allows you to deploy applications with a few clicks by using Helm.
-   Image repository \([What is Container Registry?]()\):

    -   Features high availability and high concurrency.
    -   Supports image acceleration.
    -   Supports large-scale P2P image distribution. Container Registry can concurrently distribute images to a maximum of 10,000 nodes by using an optimized image distribution process. This improves the distribution efficiency by four times.
**Note:** Self-managed image repositories may fail to respond when millions of clients attempt to pull images at the same time. Container Registry improves the reliability of image repositories, reduces maintenance workloads, and facilitates application upgrades.

-   Logging:
    -   Allows you to collect logs and deliver the log data to Log Service.
    -   Supports integration with third-party open source logging solutions.
-   Monitoring:
    -   Supports container-level and VM-level monitoring.
    -   Supports integration with third-party open source monitoring solutions. |
|Multiple node types|-   The following node types are supported:
    -   x86-based computing resources: ECS instances based on the x86 architecture
    -   Heterogeneous computing resources: GPU-accelerated ECS instances, NPU-accelerated ECS instances, and FPGA-accelerated ECS instances
    -   Bare metal computing resources: ECS bare metal instances
    -   Serverless computing resources: ACK virtual nodes
    -   Edge nodes: [Edge Kubernetes clusters](/intl.en-US/User Guide for Edge Container Service/ACK@Edge overview.md) support centralized management of cloud and edge nodes, and unified application release. This increases application release efficiency by three times.
-   The following billing methods are supported:
    -   Preemptible instance
    -   Subscription
    -   Pay-as-you-go |
|IaaS capabilities|-   Networking:
    -   Provides a high-performance network plug-in that works with Virtual Private Cloud \(VPC\) and elastic network interfaces \(ENIs\). This improves the network performance by 20% compared with common networking solutions.
    -   Supports container access control and throttling.
-   Storage:
    -   Supports Alibaba Cloud disks, Apsara File Storage NAS file systems, and Object Storage Service \(OSS\) buckets, and provides standard Container Storage Interface \(CSI\) drivers.
    -   Allows you to dynamically create and migrate volumes.
-   Load balancing:

Allows you to create Internet-facing and internal-facing Server Load Balancer \(SLB\) instances.

**Note:** If you use a self-managed Ingress to control access to a self-managed Kubernetes cluster, frequent releases may negatively affect the performance of the Ingress and increase the error rate. ACK supports SLB instances, which provide high-availability load balancing and can automatically modify network configurations to meet your business needs. This solution is widely used by a large number of users for a long period of time. It provides much higher stability and reliability than self-managed Ingresses. |
|Enterprise-level security and stability|ACK is integrated with a multi-layer security solution that secures cloud-native ACK clusters. This solution protects the underlying infrastructure, intermediate software supply chains, and top-layer runtime environments. -   Multi-layer security:
    -   Infrastructure security: ACK supports complete network isolation and end-to-end data encryption. ACK associates Alibaba Cloud accounts and Resource Access Management \(RAM\) users with the Kubernetes Role-Based Access Control \(RBAC\) system. It also provides fine-grained permission management and comprehensive auditing capabilities.
    -   Software supply chain security: ACK supports a complete DevSecOps process, covering the cloud-native delivery chain, image scanning, image signing, and image synchronization.
    -   Runtime security: ACK provides runtime security capabilities, including security policy management of applications, configuration routine inspections, runtime monitoring and alerting, and key encryption and management.
-   Default security capabilities:
    -   Provides OS images that are optimized for containerized applications and supports Kubernetes clusters and Docker versions that have high stability and enhanced security.
    -   Enhances the security compliance of cluster configurations, system components, and system images based on the Center for Internet Security \(CIS\) Benchmark and container security best practices.
    -   Grants minimum default permissions for managing cloud resources on nodes.
-   [Sandboxed-Container](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Sandboxed-Container overview.md) allows you to run an application in a sandboxed and lightweight virtual machine. This virtual machine has a dedicated kernel, isolates applications from each other, and provides enhanced security. Sandboxed-Container is suitable in scenarios such as untrusted application isolation, fault isolation, performance isolation, and load isolation among multiple users.
-   [TEE-based confidential computing](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/TEE-based confidential computing.md) is a cloud-native and all-in-one solution based on Intel Software Guard Extensions \(SGX\). This solution ensures data security, integrity, and confidentiality. Confidential computing allows you to isolate sensitive data and code by using a trusted execution environment. |
|24/7 technical support|Provides 24/7 technical support through the ticket system.|

## Disadvantages of self-managed Kubernetes clusters

-   The steps to create a Kubernetes cluster are complicated.

    You must manually configure the components, configuration files, certificates, keys, plug-ins, and tools. This process takes professional engineers up to several weeks.

-   Significant costs are required to integrate self-managed Kubernetes cluster with public cloud services.

    You must use your own resources to integrate your system with other Alibaba Cloud services, such as Log Service, monitoring services, and storage management services.

-   A container is a systematic project that involves various technologies, such as networking, storage, operating systems, and orchestration. Professional engineers are required to use containers.
-   The container technology is under constant development. To keep up with the frequent version iterations, you must continuously upgrade and test your containerized applications.

