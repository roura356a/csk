# Benefits

This topic describes the advantages of Container Service for Kubernetes \(ACK\) clusters and the disadvantages of user-created Kubernetes clusters.

## Advantages of ACK

|Advantage|Description|
|---------|-----------|
|Cluster management|-   Provides the following three types of Kubernetes clusters: dedicated clusters, managed clusters, and serverless clusters.
-   Supports up to 5,000 Elastic Compute Service \(ECS\) instances in one cluster.

**Note:** If you want to create a cluster of more than 2,000 ECS instances, submit a ticket in [Quota Center](https://quotas.console.aliyun.com/products/csk/quotas).

-   Supports multi-cluster management, cross-zone clusters, and the cluster federation feature.
-   Provides cross-zone high availability and disaster recovery. |
|Elastic resource scaling|-   Automatically scales the number of containers based on container resource usage.
-   Scales up to thousands of nodes in minutes.
-   If your application is deployed on elastic container instances \(ECIs\) in a serverless Kubernetes cluster, up to 500 pods can be started in 30 seconds.
-   Supports vertical scaling with a few clicks.
-   Supports affinity policies and scale-out for your services.
-   Provides standard Horizontal Pod Autoscaler \(HPA\), Vertical Pod Autoscaler \(VPA\), and Cluster Autoscaler capabilities provided by the Kubernetes community.
-   Provides the scheduled scaling capability similar to cron HPA and provides serverless scalability by using virtual-kubelet-autoscaler.
-   Uses ack-kubernetes-elastic-workload to provide fine-grained scheduling for online business.
-   Provides alibaba-metrics-adapter to meet different scaling needs. Application scaling is optimized by using ingress gateways, Sentinel-based microservice rate limiting, and other methods. |
|All-in-one container management|-   Application management:
    -   Supports phased release, blue-green release, application monitoring, and application autoscaling.
    -   Provides a built-in application store that allows you to deploy applications with a few clicks by using Helm.
-   Repository \([Alibaba Cloud Container Registry]()\):

    -   Provides high availability and high concurrency of image pull requests.
    -   Supports accelerated image retrieval.
    -   Supports large-scale P2P image distribution. By using the optimized image distribution process, ACK can automatically distribute images to a maximum of 10,000 nodes. This improves the distribution efficiency by four times.
**Note:** User-created image repositories may fail to respond when millions of clients attempt to pull images at the same time. Alibaba Cloud Container Registry \(ACR\) improves the reliability of image repositories and reduces the O&M and upgrade costs.

-   Logs:
    -   Allows you to collect logs and deliver the collected logs to Log Service.
    -   Supports the integration with third-party open source logging solutions.
-   Monitoring:
    -   Supports both container-level and VM-level monitoring.
    -   Supports the integration with open source monitoring solutions from third-party providers. |
|Multiple types of nodes|-   The following node types are supported:
    -   x86-based computing resources: ECS instances based on the x86 architecture
    -   Heterogeneous computing resources: GPU ECS instances, NPU ECS instances, and FPGA ECS instances
    -   Bare metal computing resources: ECS Bare Metal \(EBM\) instances
    -   Serverless computing resources: ACK virtual nodes
    -   Edge nodes: [Alibaba Cloud Container Service for Kubernetes@Edge \(ACK@Edge\)](/intl.en-US/User Guide for Edge Container Service/ACK@Edge overview.md) supports centralized management of cloud and edge nodes and unified application release. It increases the release efficiency by three times.
-   The following billing methods are supported:
    -   Preemptible instances
    -   Subscription
    -   Pay-as-you-go |
|IaaS capabilities|-   Network:
    -   Provides a high-performance plug-in to assign elastic network interfaces \(ENIs\) to pods. The performance of this plug-in is 20% higher than that of regular network solutions.
    -   Supports container access policies and throttling.
-   Storage:
    -   Supports Alibaba Cloud disks, Apsara File Storage NAS volumes, and Object Storage Service \(OSS\) buckets, and provides standard CSI drivers.
    -   Allows you to dynamically create and migrate volumes.
-   Load balancing:

Allows you to create public-facing and internal Server Load Balancer \(SLB\) instances.

**Note:** If you use user-created ingresses to control access to a user-created Kubernetes cluster, frequent service releases may negatively affect the performance of the ingress and increase the error rate. ACK allows you to create SLB instances, which provide high-availability load balancing and can automatically modify network configurations to suit your business needs. This solution is widely used by a large number of users over a long period of time. It provides much higher stability and reliability than user-created ingresses. |
|Enterprise-level security and stability|ACK is integrated with a multi-layer security solution that secures cloud-native ACK clusters. This solution protects the underlying infrastructure, intermediate software supply chains, and top-layer runtime environments.-   The end-to-end security solution ensures the security of the following resources:
    -   Infrastructure: ACK supports comprehensive network isolation and end-to-end data encryption. ACK associates Alibaba Cloud accounts and RAM users with the Kubernetes Role-Based Access Control \(RBAC\) system. It also provides fine-grained permission management and comprehensive audit capabilities.
    -   Software supply chains: ACK has a complete DevSecOps process, including image scan, cloud-native delivery chain, image signing, and image synchronization.
    -   Runtime: ACK provides runtime security capabilities, including security policy management of applications, configuration routine inspection, runtime monitoring and alerting, and key encryption and management.
-   Default security capabilities:
    -   ACK provides optimized OS images of Kubernetes containers and supports Kubernetes clusters and Docker versions that have high stability and enhanced security.
    -   Enhances the security compliance of cluster configurations, system components, and system images based on the CIS Benchmark and container security best practices.
    -   Grants minimum permissions on managing default cloud resources on nodes.
-   [Sandboxed-Container](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Sandboxed-Container overview.md) allows you to run an application in a sandboxed and lightweight virtual machine. This virtual machine has a dedicated kernel, isolates applications from each other, and provides enhanced security. Sandboxed-Container is suitable in scenarios such as untrusted application isolation, fault isolation, performance isolation, and load isolation among multiple users.
-   [TEE-based confidential computing](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/TEE-based confidential computing.md) is a cloud-native and all-in-one solution based on Intel Software Guard Extensions \(SGX\). This solution ensures security, integrity, and confidentiality of data in use. It also lowers the costs of developing, delivering, and managing trusted applications and confidential computing applications. Confidential computing allows you to isolate sensitive data and code by using a trusted execution environment. |
|24/7 technical support|Provides you with 24/7 technical support in the ticket system.|

## Disadvantages of user-created Kubernetes clusters

-   The operations to create a Kubernetes cluster are complicated.

    You must manually configure the components, configuration files, certificates, keys, plug-ins, and tools. This process takes professional engineers up to several weeks.

-   Significant costs are required to integrate user-created Kubernetes cluster with public cloud services.

    You must use your own resources to integrate your system with other Alibaba Cloud services, such as Log Service, monitoring services, and storage management services.

-   A container is a systematic project that involves various technologies, such as network, storage, operating systems, and orchestration. Professional engineers are required to use containers.
-   The container technology is under constant development. To keep up with the frequent version iterations, you must continuously upgrade and test your containerized applications.

