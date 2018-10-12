# Advantages {#concept_mws_czc_l2b .concept}

## Advantages of Container Service for Kubernetes {#section_otw_3pf_vdb .section}

**Ease of use**

-   Supports creating Kubernetes clusters with one click in the Container Service console.
-   Supports upgrading Kubernetes clusters with one click in the Container Service console.

    You may have to deal with self-built Kubernetes clusters of different versions at the same time, including version 1.8.6, 1.9.4, 1.10, and later. Upgrading clusters each time brings you great adjustments and Operation & Maintenance \(O&M\) costs. Container Service upgrade solution performs rolling update by using images and uses the backup policy of complete metadata, which allows you to conveniently roll back to the previous version.

-   Supports expanding or contracting Kubernetes clusters conveniently in the Container Service console.

    Container Service Kubernetes clusters allow you to expand or contract the capacity vertically with one click to respond to the peak of the data analysis business quickly.


**Powerful functions**

|Function|Description|
|--------|-----------|
|**Network **| -   High-performance Virtual Private Cloud \(VPC\) network plug-in.
-   Supports network policy and flow control.

 Container Service provides you with continuous network integration and the best network optimization.

 |
|**Server Load Balancer **| Supports creating Internet or intranet Server Load Balancer instances.

 If your self-built Kubernetes clusters are implemented by using the self-built Ingress, releasing the business frequently may cause pressure on Ingress configuration and higher error probabilities. The Server Load Balancer solution of Container Service supports Alibaba Cloud native high-availability Server Load Balancer, and can automatically modify and update the network configurations. This solution has been used by a large number of users for a long time, which is more stable and reliable than self-built Kubernetes.

 |
|**Storage**| Container Service integrates with Alibaba Cloud cloud disk, Network Attached Storage \(NAS\), and block storage, and provides the standard FlexVolume drive.

 Self-built Kubernetes clusters cannot use the storage resources on the cloud. Alibaba Cloud Container Service provides the best seamless integration.

 |
|**O&M**| -   Integrates with Alibaba Cloud Log Service and CloudMonitor.
-   Supports auto scaling.

 |
|**Image repository**| -   High availability. Supports high concurrency.
-   Supports speeding up the pull of images.
-   Supports P2P distribution.

 The self-built image repository may crash if you pull images from millions of clients at the same time. Enhance the reliability of the image repository by using the image repository of Alibaba Cloud Container Service, which reduces the O&M burden and upgrade pressure.

 |
|**Stability **| -   The dedicated team guarantees the stability of the container.
-   Each Linux version and Kubernetes version are provided to you after strict tests.

 Container Service provides the Docker CE to reveal all the details and promotes the repair capabilities of Docker. If you have issues such as Docker Engine hangs, network problems, and kernel compatibility, Container Service provides you with the best practices.

 |
|**High availability**| -   Supports multiple zones.
-   Supports backup and disaster recovery.

 |
|**Technical support**| -   Provides the Kubernetes upgrade capabilities. Supports upgrading a Kubernetes cluster to the latest version with one click.
-   Alibaba Cloud container team is responsible for solving problems about containers in your environment.

 |

