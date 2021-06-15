---
keyword: [Terway, network planning, Terway and Flannel, IPvlan]
---

# Use the Terway plug-in

Terway is an open source Container Network Interface \(CNI\) plug-in developed by Alibaba Cloud. Terway is built on Virtual Private Cloud \(VPC\) and allows you to regulate how containers communicate with each other by using standard Kubernetes network policies. You can use Terway to enable intercommunication within a Kubernetes cluster. This topic describes how to use Terway in a Container Service for Kubernetes \(ACK\) cluster.

Terway is a network plug-in developed by Alibaba Cloud for ACK. Terway allows you to configure networks for pods by associating Alibaba Cloud elastic network interfaces \(ENIs\) with the pods. Terway allows you to use standard Kubernetes network policies to regulate how containers communicate with each other. In addition, Terway is compatible with Calico network polices.

In a cluster that has Terway installed, each pod has a separate network stack and is assigned a separate IP address. Pods on the same Elastic Compute Service \(ECS\) instance communicate with each other by forwarding packets within the ECS instance. Pods on different ECS instances communicate with each other through an ENI in the VPC where the ECS instances are deployed. Tunneling technologies such as Virtual Extensible Local Area Network \(VXLAN\) are not required to encapsulate packets. This improves communication efficiency.

![Terway](../images/p32414.png "How Terway works")

## Terway and Flannel

When you create an ACK cluster, you can choose one of the following network plug-ins:

-   Terway: a network plug-in developed by Alibaba Cloud for ACK. Terway allows you to assign ENIs to containers and use standard Kubernetes network policies to regulate how containers communicate with each other. Terway also supports bandwidth throttling on individual containers. Terway uses flexible IP Address Management \(IPAM\) policies to allocate IP addresses to containers. This prevents the waste of IP addresses. If you do not want to use network policies, you can select Flannel as the network plug-in. Otherwise, we recommend that you select Terway.
-   Flannel: an open source CNI plug-in, which is simple and stable. You can use Flannel together with Alibaba Cloud VPC. This way, your clusters and containers can run in high-performance and stable networks. However, Flannel provides only basic features. It does not support standard Kubernetes network policies. For more information, see [Flannel](https://github.com/coreos/flannel).

|Item|Terway|Flannel|
|----|------|-------|
|Performance|The IP address of each pod in an ACK cluster is assigned from the CIDR block of the VPC where the cluster is deployed. Therefore, you do not need to use the Network Address Translation \(NAT\) service to translate IP addresses. This prevents the waste of IP addresses. In addition, each pod in the cluster can use an exclusive ENI.|Flannel works together with Alibaba Cloud VPC. The CIDR block of pods that you specify must be different from that of the VPC where the cluster is deployed. Therefore, the NAT service is required and some IP addresses may be wasted.|
|Security|Terway supports network policies.|Flannel does not support network policies.|
|IP address management|Terway allows you to assign IP addresses on demand. You do not have to assign CIDR blocks by node. This prevents the waste of IP addresses.|You must assign CIDR blocks by node. In large-scale clusters, a significant number of IP addresses may be wasted.|
|SLB|Server Load Balancer \(SLB\) directly forwards network traffic to pods. You can upgrade the pods without service interruptions.|SLB forwards network traffic to the NodePort Service. Then, the NodePort Service routes the network traffic to pods.|

## Precaution

-   To use the Terway plug-in, we recommend that you use ECS instances of higher specifications and later types, such as ECS instances that belong to the g5 or g6 instance families with at least 8 CPU cores. For more information, see [Instance families](/intl.en-US/Instance/Instance families.md).
-   The maximum number of pods that each node supports is based on the number of ENIs assigned to the node.

    -   Maximum number of pods supported by each shared ENI = \(Number of ENIs supported by each ECS instance - 1\) × Number of private IP addresses supported by each ENI
    -   Maximum number of pods supported by each exclusive ENI = Number of ENIs supported by each ECS instance -1
    **Note:** You can view the number of ENIs supported by each ECS instance in the **Instance Type** section when you create or expand a cluster. For more information about how to create a cluster, see [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md).


## Step 1: Plan the cluster network

When you create an ACK cluster, you must specify a VPC, vSwitches, the CIDR block of pods, and the CIDR block of Services. If you want to install the Terway plug-in, you must first create a VPC and two vSwitches in the VPC. The two vSwitches must be in the same zone. For more information about how to plan the network for a cluster that uses Terway, see [Plan CIDR blocks for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Plan CIDR blocks for an ACK cluster.md).

You can refer to the following table to assign CIDR blocks for a cluster that uses Terway.

|VPC CIDR block|vSwitch CIDR block|CIDR block of pod vSwitch|Service CIDR|
|--------------|------------------|-------------------------|------------|
|192.168.0.0/16|192.168.0.0/19|192.168.32.0/19|172.21.0.0/20|

**Note:**

-   The IP addresses within the CIDR block of the vSwitch are assigned to nodes.
-   The IP addresses within the CIDR block of the pod vSwitch are assigned to pods.

The following example describes how to create a VPC and two vSwitches. The CIDR blocks in the preceding table are assigned in this example.

1.  Log on to the [VPC console](https://vpcnext.console.aliyun.com/vpc).

2.  In the top navigation bar, select the region where you want to deploy the VPC and click **Create VPC**.

    **Note:** The VPC must be deployed in the same region as the cloud resources that you want to deploy.

3.  On the Create VPC page, set **Name** to vpc\_192\_168\_0\_0\_16 and enter 192.168.0.0/16 in the **IPv4 CIDR Block** field.

4.  In the **Create VSwitch** section, set the name to node\_switch\_192\_168\_0\_0\_19, select a zone for the vSwitch, and then set **IPv4 CIDR Block** to **192.168.0.0/19**. Click **Add** to create the pod vSwitch.

    ![vSwitch](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0258424161/p212508.png)

    **Note:** Make sure that the two vSwitches are in the same zone.

5.  In the **Create VSwitch** section, configure the pod vSwitch. Set the name to **pod\_switch\_192\_168\_32\_0\_19** and IPv4 CIDR Block to **192.168.32.0/19**.

6.  Click **OK**.


## Step 2: Configure networks for a cluster that uses Terway

To install Terway in a cluster and configure networks for the cluster, set the following parameters.

![terway](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0258424161/p212509.png)

**Note:** A standard managed Kubernetes cluster is used as an example to show how to configure networks for a cluster that uses Terway as the network plug-in. For more information about how to create a cluster, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

-   **VPC**: Select the VPC created in [Step 1: Plan the cluster network](#section_2nd_fho_f25).
-   **VSwitch**: Select the vSwitch created in [Step 1: Plan the cluster network](#section_2nd_fho_f25).
-   **Network Plug-in**: Select **Terway**.

    If you set **Network Plug-in** to **Terway**, you must set **Terway Mode**.

    -   Select or clear **Assign One ENI to Each Pod**.

-   If you select the check box, an ENI is assigned to each pod.
-   If you clear the check box, an ENI is shared among multiple pods. A secondary IP address that is provided by the ENI is assigned to each pod.
**Note:** To select the **Assign One ENI to Each Pod** check box, you must [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to apply for this option to be available for your account.

    -   Select or clear **IPVLAN**.

-   This option is available only when you clear Assign One ENI to Each Pod.
-   If you select IPVLAN, IPVLAN and extended Berkeley Packet Filter \(eBPF\) are used for network virtualization when an ENI is shared among multiple pods. This improves network performance. Only the Alibaba Cloud Linux 2 operating system is supported.
-   If you clear IPVLAN, policy-based routes are used for network virtualization when an ENI is shared among multiple pods. The CentOS 7 and Alibaba Cloud Linux 2 operating systems are supported. This is the default setting.
        For more information about the IPVLAN feature in Terway mode, see [Terway IPvlan](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Use the Terway plug-in.md).

    -   Select or clear **Support for NetworkPolicy**.

-   The NetworkPolicy feature is available only when you clear Assign One ENI to Each Pod. By default, Assign One ENI to Each Pod is unselected.
-   If you select Support for NetworkPolicy, you can use Kubernetes network policies to control the communication among pods.
-   If you clear Support for NetworkPolicy, you cannot use Kubernetes network policies to control the communication among pods. This prevents Kubernetes network policies from overloading the Kubernetes API server.
-   **Pod VSwitch**: Select the pod vSwitch created in [Step 1: Plan the cluster network](#section_2nd_fho_f25).
-   **Service CIDR**: Use the default settings.

## Terway IPvlan

If you select the Terway network plug-in when you create a cluster, you can choose to enable the Terway IPvlan mode. The Terway IPvlan mode provides high-performance networks for pods and Services based on IPvlan and Extended Berkeley Packet Filter \(eBPF\) technologies.

Compared with the default Terway mode, the Terway IPvlan mode optimizes the performance of pod networks, Service networks, and network policies.

-   Pod networks are directly implemented based on the sub-interfaces of elastic network interfaces \(ENIs\) in IPvlan L2 mode. This significantly simplifies network forwarding on the host and reduces the latency by 30% compared with the traditional mode. The performance of pod networks is almost the same as that of the host network.
-   Service networks are implemented based on the eBPF technology instead of the kube-proxy mode. Traffic forwarding no longer depends on the host iptables or IP Virtual Server \(IPVS\). This maintains almost the same performance in larger-scale clusters and offers better scalability. Compared with traffic forwarding based on IPVS and iptables, this new approach greatly reduces network latency in scenarios that involve a large number of new connections and port reuse.
-   The network policies of pods are implemented based on the eBPF technology instead of iptables. This way, large numbers of iptables rules are no longer generated on the host and the impact of network policies on network performance is minimized.

**Limits of the Terway IPvlan mode**

-   Only the Alibaba Cloud Linux 2 operating system is supported.
-   The Sandboxed-Container runtime is not supported.
-   The implementation of network policies is different from when the default Terway mode is used.
    -   The CIDR selector has a lower priority than the pod selector. Additional pod selectors are required if the CIDR block of pods is within the CIDR range specified by the CIDR selector.
    -   The except keyword of the CIDR selector is not fully supported. We recommend that you do not use the except keyword.
    -   If you use a network policy of the Egress type, you cannot access pods in the host network or the IP addresses of nodes in the cluster.
-   You may fail to access the Internet-facing SLB instance that is associated with a LoadBalancer Service from within the cluster due to loopback issues. For more information, see [Why am I unable to access an SLB instance?](/intl.en-US/FAQ/Why am I unable to access an SLB instance?.md).

**Scenarios**

-   Middleware and microservices

    Avoids performance degradation in large-scale deployments and reduces the network latency of microservices.

-   Gaming and live streaming applications

    Significantly reduces network latency and resource competition among multiple instances.

-   High-performance computing

    High-performance computing requires high-throughput networks. The Terway IPvlan mode reduces CPU overhead and saves computing resources for core workloads.


**Related topics**  


[Overview](/intl.en-US/User Guide for Kubernetes Clusters/Network/Overview.md)

[Plan CIDR blocks for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Plan CIDR blocks for an ACK cluster.md)

[Create vSwitches for an ACK cluster that has Terway installed](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Create vSwitches for an ACK cluster that has Terway installed.md)

