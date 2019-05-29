# General FAQ {#concept_371621 .concept}

## Q: What is Alibaba Cloud Container Service for Kubernetes? {#section_64l_t3z_zsh .section}

Alibaba Cloud Container Service for Kubernetes \(ACK\) is a fully-managed service compatible with Kubernetes. ACK helps you focus on your applications, eliminating the need to manage the container infrastructure.

It provides enterprise-level high-performance and flexible management of Kubernetes containerized applications throughout the application lifecycle. This service simplifies cluster creation and expansion and integrates Alibaba Cloud capabilities in virtualization, storage, network, and security, providing an improved running environment for Kubernetes containerized applications.

## Q: When should I use ACK? {#section_i09_7wu_qik .section}

ACK fits perfectly for scenarios such as DevOps continuous delivery, Microservice architecture, hybrid cloud architecture and auto-scaling. Here we take auto scaling as an example: ACK can automatically scale out or in the application according to the incoming traffic, without manual intervention. In this way, the system is elastic and stable when facing traffic surge, and resource utility rate gets improved as well.

## Q: How does ACK work? {#section_mhz_0vk_s1f .section}

Container Service for Kubernetes is adapted and enhanced on the basis of native Kubernetes. This service simplifies cluster creation, scaling and upgrade, and integrates Alibaba Cloud capabilities in computing, storage, network, and security, providing an improved running environment for Kubernetes containerized applications.

## Q: What are the advantages of ACK over a local host in creating a Kubernetes cluster? {#section_qd5_uau_tro .section}

-   Ease of use
    -   Supports creating Kubernetes clusters with one click in the Container Service console.
    -   Supports upgrading Kubernetes clusters with one click in the Container Service console.

        You may have to deal with self-built Kubernetes clusters of different versions at the same time, including version 1.11.x, 1.12.x, and later. Container Service upgrade solution performs rolling update by using images and uses the backup policy of complete metadata, which allows you to conveniently roll back to the previous version.

    -   Supports scaling Kubernetes clusters conveniently in the Container Service console.

        Container Service Kubernetes clusters allow you to expand or contract the capacity vertically with one click to respond to the peak of the data analysis business quickly.

-   Powerful functions

    |Function|Description|
    |--------|-----------|
    |Network|     -   High-performance Virtual Private Cloud \(VPC\) network plug-in.
    -   Supports network policy and flow control.
    -   Container Service provides you with continuous network integration and the best network optimization.
 |
    |Server Load Balancer|     -   Supports creating Internet or intranet Server Load Balancer instances.
    -   If your self-built Kubernetes clusters are implemented by using the self-built Ingress, releasing the business frequently may cause pressure on Ingress configuration and higher error probabilities. The Server Load Balancer solution of Container Service supports Alibaba Cloud native high-availability Server Load Balancer, and can automatically modify and update the network configurations. This solution has been used by a large number of users for a long time, which is more stable and reliable than self-built Kubernetes.
 |
    |Storage| Container Service integrates with Alibaba Cloud cloud disk, Network Attached Storage \(NAS\), and block storage, and provides the standard FlexVolume drive.

 Self-built Kubernetes clusters cannot use the storage resources on the cloud. Alibaba Cloud Container Service provides the best seamless integration.

 |
    |O&M|     -   Integrates with Alibaba Cloud Log Service and CloudMonitor.
    -   Supports auto scaling.
 |
    |Image repository|     -   Provides high availability.
    -   Supports image scan.
    -   Supports P2P distribution and replication across regions.
 The self-built image repository may crash if you pull images from millions of clients at the same time. Enhance the reliability of the image repository by using the image repository of Alibaba Cloud Container Service, which reduces the O&M burden and upgrade pressure.

 |
    |Stability|     -   The dedicated team guarantees the stability of the container.
    -   Each Linux version and Kubernetes version are provided to you after strict tests.
    -   Container Service provides the Docker CE to reveal all the details and promotes the repair capabilities of Docker. If you have issues such as Docker Engine hangs, network problems, and kernel compatibility, Container Service provides you with the best practices.
 |
    |High availability|     -   Supports multiple zones.
    -   Supports backup and disaster recovery.
 |
    |Technical support|     -   Provides the Kubernetes upgrade capabilities. Supports upgrading a Kubernetes cluster to the latest version with one click.
    -   Alibaba Cloud container team is responsible for solving problems about containers in your environment.
 |


## Q: How can I get started with ACK? {#section_h5u_pes_gn4 .section}

Please see [使用流程](../../../../intl.zh-CN/快速入门/使用流程.md#) for more details on how to get started with ACK.

