---
keyword: [Terway, Network planning, Terway and Flannel]
---

# Work with Terway

Terway is an open source Container Network Interface \(CNI\) plug-in developed by Alibaba Cloud. Terway works with Virtual Private Cloud \(VPC\) and allows you to use standard Kubernetes network policies to regulate how containers communicate with each other. You can use Terway to enable intercommunication within a Kubernetes cluster. This topic describes how to use Terway in a Container Service for Kubernetes \(ACK\) cluster.

Terway is a network plug-in developed by Alibaba Cloud for ACK. Terway allows you to set up networks for pods by associating Alibaba Cloud elastic network interfaces \(ENIs\) with the pods. Terway allows you to use standard Kubernetes network policies to regulate how containers communicate with each other. In addition, Terway is compatible with Calico network polices.

In a cluster that has Terway installed, each pod has a separate network stack and is assigned a separate IP address. Pods on the same Elastic Compute Service \(ECS\) instance communicate with each other by forwarding packets inside the ECS instance. Pods on different ECS instances communicate with each other through an ENI in the VPC where the ECS instances are deployed. This improves communication efficiency because no tunneling technologies such as Virtual Extensible Local Area Network \(VXLAN\) are required to encapsulate packets.

![Terway](../images/p32414.png "How Terway works")

## Comparison between Flannel and Terway

When you create an ACK cluster, you can choose one of the following network plug-ins:

-   Terway: a network plug-in developed by Alibaba Cloud for ACK. Terway allows you to assign ENIs to containers and use standard Kubernetes network policies to regulate how containers communicate with each other. Terway also supports bandwidth throttling on individual containers. Terway uses flexible IP Address Management \(IPAM\) policies to allocate IP addresses to containers. This avoids IP address waste. If you do not want to use network policies, you can select Flannel as the network plug-in. Otherwise, we recommend that you select Terway.
-   Flannel: an open source CNI plug-in, which is simple and stable. You can use Flannel with VPC of Alibaba Cloud. This allows your clusters and containers to run in high-performance and stable networks. However, Flannel provides only basic features. It does not support standard Kubernetes network policies. For more information, see [Flannel](https://github.com/coreos/flannel).

|Item|Terway|Flannel|
|----|------|-------|
|Performance|The IP address of each pod in an ACK cluster is assigned from the CIDR block of the VPC where the cluster is deployed. Therefore, you do not need to use the Network Address Translation \(NAT\) service to translate IP addresses. This avoids IP address waste. In addition, each pod in the cluster can use an exclusive ENI.|Flannel works with VPC of Alibaba Cloud. The CIDR block of pods that you specify must be different from that of the VPC where the cluster is deployed. Therefore, the NAT service is required and some IP addresses may be wasted.|
|Security|Terway supports network policies.|Flannel does not support network policies.|
|IP address management|Terway allows you to assign IP addresses on demand. You do not have to assign CIDR blocks by node. This avoids IP address waste.|You can only assign CIDR blocks by node. In large clusters, a great number of IP addresses may be wasted.|
|SLB|Server Load Balancer \(SLB\) directly forwards network traffic to pods. You can upgrade the pods without service interruption.|SLB forwards network traffic to the NodePort Service. Then, the NodePort Service routes the network traffic to pods.|

## Considerations

-   To use the Terway plug-in, we recommend that you use ECS instances of higher specifications and later types, such as ECS instances that belong to the g5 or g6 instance family with at least eight CPU cores. For more information, see [Instance families](/intl.en-US/Instance/Instance families.md).
-   The maximum number of pods that each node supports is based on the number of ENIs assigned to the node.

    -   Maximum number of pods supported by each shared ENI = \(Number of ENIs supported by each ECS instance - 1\) × Number of private IP addresses supported by each ENI
    -   Maximum number of pods supported by each exclusive ENI = Number of ENIs supported by each ECS instance -1
    **Note:** You can view the number of ENIs supported by each ECS instance in the **Instance Type** section when you create or expand a cluster. For more information about how to create a cluster, see [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Introduction/Create a professional managed Kubernetes cluster.md).


## Step 1: Plan CIDR blocks

When you create an ACK cluster, you must specify a VPC, vSwitches, the CIDR block of pods, and the CIDR block of Services. If you want to install the Terway plug-in, you must first create a VPC and two vSwitches in the VPC. The two vSwitches must be in the same zone. For more information about how to plan CIDR blocks for a cluster that uses Terway, see [Plan CIDR blocks for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Plan CIDR blocks for an ACK cluster.md).

You can refer to the following table to assign CIDR blocks for a cluster that uses Terway.

|VPC CIDR block|vSwitch CIDR block|CIDR block of pod vSwitch|Service CIDR|
|--------------|------------------|-------------------------|------------|
|192.168.0.0/16|192.168.0.0/19|192.168.32.0/19|172.21.0.0/20|

**Note:**

-   The CIDR block of the vSwitch. IP addresses within this CIDR block are assigned to nodes.
-   The CIDR block of the pod vSwitch. IP addresses within this CIDR block are assigned to pods.

The following example describes how to create a VPC and two vSwitches. The CIDR blocks in the preceding table are assigned in this example.

1.  Log on to the [VPC console](https://vpcnext.console.aliyun.com/vpc).

2.  In the top navigation bar, select the region where you want to deploy the VPC and click **Create VPC**.

    **Note:** The VPC must be deployed in the same region as the cloud resources that you want to deploy.

3.  On the Create VPC page, set **Name** to vpc\_192\_168\_0\_0\_16 and enter 192.168.0.0/16 in the **IPv4 CIDR Block** field.

4.  In the **Create VSwitch** section, set the name to **node\_switch\_192\_168\_0\_0\_19**, select a zone for the vSwitch, and then set IPv4 CIDR Block to **192.168.0.0/19**. Click **Add** to create the pod vSwitch.

    ![vSwitch](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0258424161/p212508.png)

    **Note:** Make sure that the two vSwitches are in the same zone.

5.  In the **Create VSwitch** section, configure the pod vSwitch. Set the name to **pod\_switch\_192\_168\_32\_0\_19** and IPv4 CIDR Block to **192.168.32.0/19**.

6.  Click **OK**.


## Step 2: Set up networks for a cluster that uses Terway

To install Terway in a cluster and set up networks for the cluster, set the following parameters.

![terway](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0258424161/p212509.png)

**Note:** A standard managed Kubernetes cluster is used as an example to show how to set up networks for a cluster that uses Terway as the network plug-in. For more information about how to create a cluster, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

-   **VPC**: Select the VPC created in [Step 1: Plan CIDR blocks](#section_2nd_fho_f25).
-   **VSwitch**: Select the vSwitch created in [Step 1: Plan CIDR blocks](#section_2nd_fho_f25).
-   **Network Plug-in**: Select **Terway**.

    If you select **Terway** for **Network Plug-in**, you must set **Terway Mode**.

    -   Select or clear **Assign One ENI to Each Pod**.

-   If you select the check box, an ENI is assigned to each pod.
-   If you clear the check box, an ENI is shared among multiple pods. A secondary IP address that is provided by the ENI is assigned to each pod.
    -   Select or clear **IPVLAN**.

-   This option is available only when you clear Assign One ENI to Each Pod.
-   If you select IPVLAN, IPVLAN and extended Berkeley Packet Filter \(eBPF\) are used for network virtualization when an ENI is shared among multiple pods. This improves network performance. Only the Alibaba Cloud Linux 2 operating system is supported.
-   If you clear IPVLAN, policy-based routes are used for network virtualization when an ENI is shared among multiple pods. The CentOS 7 and Alibaba Cloud Linux 2 operating systems are supported. This is the default setting.
**Note:** To select the **Assign One ENI to Each Pod** option,you must [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to apply for this option to be available for your account.

    -   Select or clear **Support for NetworkPolicy**.

-   The NetworkPolicy feature is available only when you clear Assign One ENI to Each Pod. By default, Assign One ENI to Each Pod is unselected.
-   If you select Support for NetworkPolicy, you can use Kubernetes network policies to control communication among pods.
-   If you clear Support for NetworkPolicy, you cannot use Kubernetes network policies to control communication among pods. This prevents Kubernetes network policies from overloading on the Kubernetes API server.
-   **Pod VSwitch**: Select the pod vSwitch created in [Step 1: Plan CIDR blocks](#section_2nd_fho_f25).
-   **Service CIDR**: Use the default settings.

**Related topics**  


[Overview](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Overview.md)

[Plan CIDR blocks for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Plan CIDR blocks for an ACK cluster.md)

[Create vSwitches for an ACK cluster that has Terway installed](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Container network/Create vSwitches for an ACK cluster that has Terway installed.md)

