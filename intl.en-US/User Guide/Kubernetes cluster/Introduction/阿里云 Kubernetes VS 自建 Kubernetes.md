# Alibaba Cloud Kubernetes vs. self-built Kubernetes {#concept_hgc_gpf_vdb .concept}

## Advantages of Alibaba Cloud Kubernetes {#section_otw_3pf_vdb .section}

**Convenient**

-   Supports creating Kubernetes clusters with one click in the Container Service console.
-   Supports upgrading Kubernetes clusters with one click in the Container Service console.

    You may have to deal with self-built Kubernetes clusters of different versions at the same time, including version 1.8.6, 1.9.4, and 1.10 in the future. Upgrading clusters each time brings you great adjustments and Operation & Maintenance \(O&M\) costs. Container Service upgrade solution performs rolling update by using images and uses the backup policy of complete metadata, which allows you to conveniently roll back to the previous version.

-   Supports expanding or contracting Kubernetes clusters conveniently in the Container Service console.

    Container Service Kubernetes clusters allow you to expand or contract the capacity vertically with one click to respond to the peak of the data analysis business quickly.


**Strong**

|Function|Description|
|--------|-----------|
|**Network**| -   High-performance Virtual Private Cloud \(VPC\) network plug-in.
-   Supports network policy and flow control.

 Container Service can provide you with continuous network integration and the best network optimization.

 |
|**Server Load Balancer**| Supports creating Internet or intranet Server Load Balancer instances.

 If your self-built Kubernetes clusters are implemented by using the self-built Ingress, publishing the business frequently may cause the Ingress to have pressure about configuration and higher error probabilities. The Server Load Balancer solution of Container Service supports Alibaba Cloud native high-availability Server Load Balancer, and can automatically modify and update the network configurations. This solution has been used by a large number of users for a long time, which is more stable and reliable than self-built Kubernetes.

 |
|**Storage**| Container Service integrates with Alibaba Cloud cloud disk, NAS, and EBS, and provides the standard FlexVolume drive.

 Self-built Kubernetes clusters cannot use the storage resources on the cloud. Alibaba Cloud Container Service provides the best seamless integration.

 |
|**O&M**| -   Integrates with Alibaba Cloud Log Service and CloudMonitor.
-   Supports auto scaling.

 |
|**Image repository**| -   High availability. Supports high concurrency.
-   Supports speeding up the pull of images.
-   Supports P2P distribution.

 The self-built image repository may crash if you pull images from millions of clients at the same time. Enhance the reliability of the image repository by using the Apsara Stack version of Container Service image repository, which reduces the O&M burden and upgrade pressure.

 |
|**Stable**| -   Special teams to guarantee the stability of containers.
-   Each Linux version and Kubernetes version are provided to you after the strict test.

 Container Service provides the Docker CE to reveal all the details and promotes the repair capabilities of Docker. If you have issues such as Docker Engine hang, network problems, and kernel compatibility, Container Service provides you with the best practices.

 |
|**High availability**| -   Supports multiple zones.
-   Supports backup and disaster recovery.

 |
|**Technical support**| -   Provides the Kubernetes upgrade capabilities. Supports upgrading a Kubernetes cluster to the latest version with one click.
-   Alibaba Cloud container team is responsible for solving problems about containers in your environment.

 |

## Costs and risks of self-built Kubernetes {#section_mw1_w1j_zdb .section}

-   Building clusters is complicated

    You must manually configure the components, configuration files, certificates, keys, plug-ins, and tools related to Kubernetes. It takes several days or weeks for professional personnel to build the cluster.

-   For public cloud, it takes you significant costs to integrate with cloud products.

    You must devote your own money to integrate with other products of Alibaba Cloud, such as Log Service, monitoring service, and storage management.

-   The container is a systematic project, involving network, storage, operating system, orchestration, and other technologies, which requires the devotion of professional personnel.
-   The container technology is continuously developing and the version iteration is fast, which requires continuous upgrade and test.

