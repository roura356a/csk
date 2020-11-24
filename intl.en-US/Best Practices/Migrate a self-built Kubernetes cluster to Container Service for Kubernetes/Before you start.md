# Before you start

For purposes of regulatory compliance and security, clusters of Container Service for Kubernetes \(ACK\) are different from self-managed Kubernetes clusters in some configurations. We recommend that you read this topic before you use ACK.

## Cluster planning

ACK supports Elastic Compute Service \(ECS\) Bare Metal instances. You can also use GPU-accelerated ECS Bare Metal instances based on your business requirements.

-   ECS Bare Metal instances

    ECS Bare Metal Instance is a compute service that is designed based on the state-of-the-art virtualization 2.0 technology developed by Alibaba Cloud. ECS Bare Metal instances combine the elasticity of virtual machines and the performance and features of physical machines. For example, ECS Bare Metal instances provide exclusive computing resources, chip-level security, confidential computing, and compatibility with multiple private clouds. ECS Bare Metal instances also support processors with heterogeneous instruction sets.

-   GPU-accelerated ECS Bare Metal instances

    GPU-accelerated ECS Bare Metal instances are suitable for GPU-related computing scenarios, such as AI, deep learning, video processing, scientific computing, and data visualization.


## Network planning

-   The network type for ECS instances in an ACK cluster: virtual private cloud \(VPC\) or classic network.
    -   VPC: We recommend that you use this network type.

        VPCs implement isolation at Layer 2 and provide improved security and flexibility than classic networks.

    -   Classic network

        Classic networks implement isolation at Layer 3. All instances in a classic network are deployed in a shared infrastructure network.

-   The network plug-in to control communication among pods in an ACK cluster: Terway or Flannel.
    -   Terway: We recommend that you use this network plug-in.

        Terway is a network plug-in that is developed by Alibaba Cloud. Terway allows you to assign elastic network interfaces \(ENIs\) to containers. It also allows you to customize Kubernetes network policies to control intercommunication among containers, and implement bandwidth throttling on individual containers. If you do not need to use Kubernetes network policies, you can use Flannel as the network plug-in. In other cases, we recommend that you use Terway.

    -   Flannel

        Flannel is a simple and stable Container Network Interface \(CNI\) plug-in that is developed by the Kubernetes community. You can use Flannel with virtual private clouds \(VPCs\) of Alibaba Cloud. This enables your clusters and containers to run in a high-speed and stable network. Flannel does not support standard Kubernetes network policies.


## Capacity planning

-   Basic capacity: When you create an ACK cluster, you can set a basic capacity based on your business requirements and budget.
-   Scalable capacity: After an ACK cluster is created, you can configure auto scaling to scale the cluster as required.
    -   horizontal pod autoscaling

        ACK supports Horizontal Pod Autoscaling \(HPA\). After HPA is enabled, an ACK cluster can automatically increase or reduce the number of application pods.

    -   cluster-autoscaler

        ACK provides cluster-autoscaler for nodes to automatically scale in and out. Regular instances, GPU-accelerated instances, and preemptible instances can be automatically added to or removed from an ACK cluster based on your requirements. This feature is applicable to instances that are deployed across multiple zones and diverse instance types, and also supports different scaling modes.


## Data planning

-   Database

    Relational Database Service \(RDS\) is a stable and reliable online database service that supports auto scaling. Based on Apsara Distributed File System and high-performance SSDs of Alibaba Cloud, RDS supports the MySQL, SQL Server, PostgreSQL, PPAS, and MariaDB TX database engines.

-   Storage

    Object Storage Service \(OSS\) is a cloud storage service that features large capacity, high security, cost-effectiveness, and high reliability.

-   Container images

    Container Registry provides a secure service for the secure maintenance and lifecycle management of application images. It allows you to build images from code repositories in China and overseas, perform security scans on images to detect potential risks, and simplify licensing on images. Container Registry provides instances of Container Registry Enterprise Edition and default instances.


## Security planning

-   Infrastructure security

    You can set security group rules to secure the infrastructure of an ACK cluster.

-   Image security

    ACK allows you to use private images and perform security scans on images to detect potential risks.

-   Kubernetes application security

    ACK supports Kubernetes network policies to control communications among applications.


## Basic O&M

-   Monitoring: You can use Cloud Monitor or Application Real-Time Monitoring Service \(ARMS\) to monitor ACK clusters and applications. You can also set alert rules based on monitoring metrics.
    -   Cloud Monitor is a service that is provided by Alibaba Cloud for you to monitor Alibaba Cloud resources. In the Cloud Monitor console, you can quickly check the status and performance of the monitored resources. Cloud Monitor allows you to monitor sites and Alibaba Cloud services. You can also customize metrics and alert rules. The Cloud Monitor console provides monitoring statistics and charts to show the status of monitored services. In addition, Cloud Monitor sends notifications of cluster exceptions based on the alert rules and monitoring metrics.
    -   ARMS is an application performance management \(APM\) service that is provided by Alibaba Cloud. It consists of frontend monitoring, application monitoring, and Prometheus monitoring. ARMS allows you to monitor and diagnose both the frontend and the backend, including browsers, mini programs, applications, distributed applications, and containers. This improves the efficiency of operations and maintenance \(O&M\) on applications.
-   Logs: You can use Log Service instead of custom logging solutions to collect logs from ACK clusters. For more information, see [Log Service](/intl.en-US/Product Introduction/What is Log Service?.md).

