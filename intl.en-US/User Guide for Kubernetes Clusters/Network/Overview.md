---
keyword: [networking, CNI, Service, Ingress]
---

# Overview

Container Service for Kubernetes \(ACK\) provides stable and high-performance container networks by integrating Kubernetes networking, Virtual Private Cloud \(VPC\), and Server Load Balancer \(SLB\). This topic describes the important terms involved in ACK cluster networking and Alibaba Cloud network infrastructure, such as container network interface \(CNI\), Service, Ingress, and DNS Service Discovery \(DNS-SD\). Understanding these terms helps you optimize application deployment models and network access methods.

## CNI

![CNI](../images/p229414.png "Container network model")

Containerized applications deploy multiple workloads on a node. Each workload requires a unique network namespace. To avoid network conflicts, each pod must have a unique network namespace. To enable the application that runs on a pod to communicate with other networks, the pod must be able to access other networks. Container networking has the following features:

-   A pod has a unique network namespace and IP address. Applications that run on different pods can listen on the same port without conflicts.
-   Pods can access each other by using their IP addresses.

    In a cluster, a pod can communicate with other applications by using a unique IP address.

    -   Pods can access each other within a cluster.
    -   Pods can access Elastic Compute Service \(ECS\) instances that are deployed in the same VPC.
    -   ECS instances can access pods that are deployed in the same VPC.
    **Note:** To ensure that pods can access ECS instances in the same VPC and ECS instances can access pods in the same VPC, you must configure correct security group rules. For more information about how to configure security group rules, see [Add security group rules](/intl.en-US/Security/Security groups/Add security group rules.md).


ACK provides two network plug-ins to help you implement container networking: Flannel and Terway. The two network plug-ins adopt different network models, which have the following features:



For more information about the CIDR blocks of VPC and Kubernetes clusters, see [t16651.md\#section\_oy4\_yg4\_vdb](/intl.en-US/User Guide for Kubernetes Clusters/Network/Plan CIDR blocks for an ACK cluster.md).

For more information about how to select a network plug-in based on your business requirements, see [Work with Terway](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Work with Terway.md).

## Service

Cloud-native applications usually require agile iterations and rapid elasticity. Single containers and the related network resources have short lifecycles. This necessitates stable addresses and automatic load balancing to achieve rapid elasticity. In ACK, a Service provides a set of pods with a stable address and balances the loads across the pods. How a Service works

-   When you create a Service, ACK assigns a stable IP address to the Service.
-   You use the selector field to select a set of pods for the Service. The Service serves as a load balancer and forwards traffic that is sent to the Service IP and port to the IP addresses and ports of the selected pods.

ACK provides the following types of Services to handle requests from different sources and clients:

-   -   **ClusterIP**

    A ClusterIP Service is used for access within the cluster. If you want your application to provide services within the cluster, create a ClusterIP Service.

    **Note:** The ClusterIP type is the default Service type.

-   -   **NodePort**

    A NodePort Service is used to expose an application to the Internet. You can use the IP address and port of a cluster node to expose your application. This way, your application can be accessed by using the node IP address and port.

-   -   **LoadBalancer**

    A LoadBalancer Service is also used to expose an application to the Internet. However, an SLB instance is used to expose your application, which provides higher availability and performance than a NodePort Service. For more information about how to use a LoadBalancer Service to expose an application, see [Use an existing SLB instance to expose an application](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Use an existing SLB instance to expose an application.md) and [Use an automatically created SLB instance to expose an application](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Use an automatically created SLB instance to expose an application.md).

-   -   **Headless Service**

    A Headless Service is defined by setting the clusterIP field to None in the Service configuration file. A Headless Service has no stable virtual IP address \(VIP\). When a client accesses the domain of the Service, DNS returns the IP addresses of all backend pods. The client must use DNS load balancing to balance the loads across pods.

-   -   **ExternalName**

    An ExternalName Service is used to map an external domain to a Service within the cluster. For example, you can map the domain of an external database to a Service name within the cluster. This allows you to access the database within the cluster by using the Service name.


For more information about how to configure a LoadBalancer Service, see [Considerations for configuring a LoadBalancer type Service](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Considerations for configuring a LoadBalancer type Service.md).

## Ingress

In ACK clusters, Services support Layer 4 load balancing. However, Ingresses manage external access to Services in the cluster at Layer 7. You can use Ingresses to configure different Layer 7 forwarding rules. For example, you can forward requests to different Services based on domain name or access path. This way, Layer 7 load balancing is implemented. For more information, see [Overview](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Overview.md).

![The relationship between Ingress and Service](../images/p230649.png "The relationship between Ingress and Service")

**Example**

In the common architecture pattern that separates frontend and backend, different access paths are used to access frontend and backend services. In this scenario, Ingresses can be used to implement Layer 7 load balancing across different application services.

![ingress](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0941397161/p230655.png)

## DNS Service Discovery

ACK uses DNS for service discovery. For example, a client can resolve the DNS name of a Service to obtain the cluster IP of the Service, or resolve the DNS name of a pod that is created by a StatefulSet to obtain the IP address of the pod. Using DNS for service discovery allows you to invoke applications by using DNS names, regardless of IP addresses or deployment environments.

CoreDNS automatically converts the DNS name of a Service to the IP address of the Service. This allows you to use the same connection address in different deployment environments. For more information about how to configure and optimize DNS resolution in a cluster, see [Introduction and configuration of the DNS service in ACK clusters](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Introduction and configuration of the DNS service in ACK clusters.md).

![dns](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0941397161/p230696.png)

## Infrastructure

-   -   **VPC**

    VPC is a type of private network provided by Alibaba Cloud. VPCs are logically isolated from one other. You can create and manage cloud services in VPCs, such as ECS instances, ApsaraDB RDS, and SLB instances.

    Each VPC consists of one vRouter, at least one private CIDR block, and at least one vSwitch.

    ![vpc](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0941397161/p230750.png)

    -   Private CIDR blocks

        When you create a VPC and a vSwitch, you must specify the private IP address range for the VPC in CIDR notation.

        You can use the standard private CIDR blocks listed in the following table and their subsets as CIDR blocks for your VPCs. For more information, see [Plan and design a VPC](/intl.en-US/Quick Start/Plan and design a VPC.md). For more information, see the Plan and design a VPC topic in *User Guide*.

        |CIDR blocks|Number of available private IP addresses \(system reserved ones excluded\)|
        |:----------|:-------------------------------------------------------------------------|
        |192.168.0.0/16|65,532|
        |172.16.0.0/12|1,048,572|
        |10.0.0.0/8|16,777,212|

    -   vRouters

        A vRouter is the hub of a VPC and serves as a gateway between the VPC and other networks. After a VPC is created, a vRouter is automatically created for the VPC. Each vRouter is associated with a route table.

        For more information, see [Overview]().

        For more information, see the Route table overview topic in *User Guide*.

    -   vSwitches

        A vSwitch is a basic network component that connects different cloud resources in a VPC. After you create a VPC, you can create a vSwitch to divide your VPC into multiple subnets. vSwitches deployed in a VPC can communicate with each other over the private network. You can deploy your applications in vSwitches that belong to different zones to improve service availability.

        For more information, see [vSwitches](/intl.en-US/VPCs and vSwitchs/Work with vSwitches.md).

        For more information, see the Create a vSwitch topic in *User Guide*.

-   -   **SLB**

    After you connect ECS instances to an SLB instance, SLB uses virtual IP addresses \(VIPs\) to virtualize the ECS instances and adds the ECS instances to an application service pool. The application service pool features high performance and high availability. Client requests are distributed across the ECS instances based on forwarding rules.

    SLB checks the health status of the ECS instances and automatically removes unhealthy ECS instances from the pool to eliminate single points of failure. This improves the availability of your applications. You can also use SLB to defend your applications against distributed denial of service \(DDoS\) attacks.

    SLB consists of the following components:

    -   SLB instance

        An SLB instance is a running SLB service entity that receives and distributes traffic to backend servers. To use SLB, you must create an SLB instance and create at least one listener and connect at least two ECS instances to the SLB instance.

    -   Listener

        A listener checks client requests, forwards the requests to backend servers, and performs health checks on backend servers.

    -   Backend server

        Backend servers are ECS instances that receive client requests that are distributed from the frontend SLB service. You can add ECS instances to a server pool, or use VServer groups or primary/secondary server groups to add and manage ECS instances in batches.


**Related topics**  


[Manage service quotas](/intl.en-US/Common configurations/Manage service quotas.md)

[Use network policies for access control](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Use network policies for access control.md)

[Plan CIDR blocks for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Plan CIDR blocks for an ACK cluster.md)

[Work with Terway](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Work with Terway.md)

[Create a security group](/intl.en-US/Security/Security groups/Create a security group.md)

