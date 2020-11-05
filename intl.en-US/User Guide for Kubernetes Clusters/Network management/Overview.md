# Overview

This topic describes networks supported by Container Service for Kubernetes \(ACK\).

## Container networks

ACK provides stable and high-performance container networks by integrating Kubernetes networks with virtual private clouds \(VPCs\) of Alibaba Cloud. ACK supports the following types of intercommunication:

-   Pods in an ACK cluster can access each other.
-   Pods can access services that are deployed in the same ACK cluster.
-   Elastic Compute Service \(ECS\) instances can access services that are deployed in the same ACK cluster.
-   \*Pods can access ECS instances that are deployed in the same VPC.
-   \*ECS instances can access pods that are deployed in the same VPC.

**Note:** The asterisk \(\*\) indicates that valid security group rules must be set to enable intercommunication.

## Network capabilities

**Service**

The following issues may occur when you access pods instead of accessing the application that runs on the pods:

-   You may fail to access pods because controllers such as Deployments in Kubernetes may delete or recreate the pods that you want to access.
-   A pod is assigned an IP address only after it is launched. Therefore, the IP address of a pod cannot be obtained before the pod is launched.
-   In most cases, an application runs on a group of pods that are deployed from the same image. It is infeasible to access an individual pod each time.

A service is an object in Kubernetes. It can be used to address the preceding issues. Each service is assigned a static IP address. A service forwards traffic destined for the IP address of the service to pods. The label selector determines to which pod the traffic is forwarded. A service also serves as a load balancer for pods. For more information, see [t16662.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Create a service.md).

**Ingress**

IP addresses of services and pods are accessible only within an ACK cluster. Requests outside an ACK cluster are forwarded by a Server Load Balancer \(SLB\) instance to NodePort that is exposed by services on each node. Then, kube-proxy discards the requests or forwards the requests to pods through edge routers. An Ingress is a collection of routing rules for forwarding requests to ACK clusters.

An Ingress provides external access to services within an ACK cluster. It can expose the URL of the cluster, function as a load balancer, terminate SSL connections, and route HTTP requests. The cluster administrator must deploy an Ingress controller to configure Ingress rules. The Ingress controller monitors changes of Ingresses and services, configures rule-based load balancing, and provides access endpoints.

Components of an Ingress

-   NGINX: distributes external requests to pods based on load balancing.
-   Ingress controller: obtains the IP addresses of the pods that correspond to the services by calling the API, and writes the IP addresses to the configuration file of NGINX.
-   Ingress: creates virtual hosts for NGINX.

For more information, see [Create an Ingress on the web UI](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Create an Ingress on the web UI.md).

## Infrastructure

**VPC**

Virtual Private Cloud \(VPC\) is a type of private network provided by Alibaba Cloud. VPCs are logically isolated from each other. You can create and manage cloud services in VPCs, such as ECS instances, ApsaraDB RDS, and SLB instances.

Each VPC consists of a CIDR block, a VRouter, and at least one VSwitch.

![VPC](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1345359951/p52056.png)

**SLB**

After you connect ECS instances to an SLB instance, SLB uses virtual IP addresses \(VIPs\) to virtualize the ECS instances and adds the ECS instances to an application service pool. The application service pool is high-performance and highly available. Client requests are distributed to the ECS instances based on forwarding rules.

SLB checks the health status of the ECS instances and automatically removes unhealthy ECS instances from the pool to eliminate single points of failure. This improves the resilience of your applications. You can also use SLB to defend your applications against distributed denial of service \(DDoS\) attacks.

SLB consists of the following components:

-   SLB instances

    An SLB instance is a running SLB service entity that receives traffic and distributes traffic to backend servers. To get started with SLB, you must create an SLB instance and create at least one listener and connect at least two ECS instances to the SLB instance.

-   Listeners

    A listener checks client requests, forwards the requests to backend servers, and performs health checks on backend servers.

-   Backend servers

    Backend servers are ECS instances that receive client requests distributed from the frontend SLB service. You can add ECS instances to a server pool, or use VServer groups or primary/secondary server groups to add and manage ECS instances in batches.


![SLB](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2345359951/p52057.png)

## Network plug-in

Terway is a network plug-in developed by ACK. It is fully compatible with Flannel. Terway allows you to perform the following operations:

-   Allocate Alibaba Cloud elastic network interfaces \(ENIs\) to containers.
-   Define access policies based on standard Kubernetes network policies for intercommunication among containers. Terway is compatible with Calico network policies.

![Network plug-in](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0545359951/p32414.png)

In a container network that has Terway installed, each pod has a separate network stack and is assigned a separate IP address. Pods on the same ECS instance communicate with each other by forwarding packets inside the ECS instance. Pods on different ECS instances communicate with each other through a VRouter in the VPC. This improves communication efficiency because no tunneling technologies such as Virtual Extensible Local Area Network \(VXLAN\) is needed to encapsulate packets.

## Network policies

Network policies define how pods communicate with each other and how pods communicate with other network endpoints.

Network policies are Kubernetes resources that select pods based on labels and define communication rules for the selected pods. For more information, see [t64520.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Use network policies for access control.md).

