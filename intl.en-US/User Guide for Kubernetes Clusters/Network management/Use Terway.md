# Use Terway

This topic describes how to use Terway in a Container Service for Kubernetes \(ACK\) cluster.

## About Terway

Terway is a network plug-in developed by ACK. It allows you to assign native elastic network interfaces \(ENIs\) to pods for pod networking:

-   Assign Alibaba Cloud ENIs and secondary IP addresses to containers.
-   Define access policies based on standard Kubernetes network policies for intercommunication among containers. Terway is compatible with Calico network policies.

![Terway network plug-in](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0545359951/p32414.png)

In a cluster that has Terway installed, each pod has a separate network stack and is assigned a separate IP address. Pods on the same ECS instance communicate with each other by forwarding packets inside the ECS instance. Pods on different ECS instances communicate with each other through an ENI in the virtual private cloud \(VPC\). This improves communication efficiency because no tunneling technologies such as Virtual Extensible Local Area Network \(VXLAN\) is needed to encapsulate packets.

## Considerations

-   To use Terway, we recommend that you use ECS instances of higher specifications and later types, such as ECS instances that belong to the g5 or g6 instance family with at least eight CPUs.
-   The maximum number of pods that each node supports is based on the number of ENIs on the node.

    -   Maximum number of pods supported by each shared ENI = \(Number of ENIs on each ECS instance - 1\) × Number of private IP addresses supported by each ENI
    -   Maximum number of pods supported by each dedicated ENI = Number of ENIs on each ECS instance -1
    **Note:** You can view the number of ENIs supported by each ECS instance in the Instance Type section when you create or expand a cluster.


## Work with Terway

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click **Create Kubernetes Cluster** in the upper-right corner of the page.

4.  On the Select Cluster Template page, click **Standard Managed Cluster**.

    **Note:** A standard managed cluster is created in this topic. For more information, see [Create a managed kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md).

5.  Select the **Terway** network plug-in.

    ![Select the network plug-in](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0545359951/p32375.png)

    If you select **Terway**, you must set **Terway Mode**.

    -   Select or clear **Assign One ENI to Each Pod**.

-   If you select the check box, an ENI is assigned to each pod.
-   If you clear the check box, an ENI is shared among multiple pods. A secondary IP address provided by the ENI is assigned to each pod.
    -   Select or clear **IPVLAN**.

-   This option is available only when you clear Assign One ENI to Each Pod.
-   If you select IPVLAN, IPVLAN and the extended Berkeley Packet Filter \(eBPF\) are used for network virtualization when an ENI is shared among multiple pods. This improves network performance. Only the Alibaba Cloud Linux 2 operating system is supported.
-   If you clear IPVLAN, policy-based routes are used for network virtualization when an ENI is shared among multiple pods. The CentOS 7 and Alibaba Cloud Linux 2 operating systems are supported. This is the default setting.
**Note:** **Assign One ENI to Each Pod** and **IPVLAN** are available to only users in the whitelist.If you are not in the whitelist, [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

    -   Specify whether to enable the **NetworkPolicy** feature.

-   The NetworkPolicy feature is available only when you clear Assign One ENI to Each Pod. By default, Assign One ENI to Each Pod is unselected.
-   If you select Support for NetworkPolicy, you can use Kubernetes network policies to control communication among pods.
-   If you clear Support for NetworkPolicy, you cannot use Kubernetes network policies to control communication among pods. This prevents Kubernetes network policies from posing heavy loads on the Kunernetes API server.

## Flannel and Terway

When you create an ACK cluster, you can select either Terway or Flannel as the network plug-in.

-   [Flannel](https://github.com/coreos/flannel): a simple and stable Container Network Interface \(CNI\) plug-in that is developed by the Kubernetes community. You can use Flannel with VPC of Alibaba Cloud. This allows your clusters and containers to run in a high-performance and stable network. However, Flannel provides only basic features. It does not support standard Kubernetes network policies.
-   Terway: a network plug-in developed by ACK. Terway allows you to assign ENIs to containers and define access policies based on standard Kubernetes network policies for intercommunication among containers. Terway also supports bandwidth throttling on individual containers. If you do not need to use network policies, you can select Flannel as the network plug-in. Otherwise, we recommend that you select Terway.

    **Note:**

    -   Terway integrates the Felix component of Calico to enforce network policies. Therefore, you can use Terway to create network policies as you do with Calico. For users that use Calico in self-managed clusters, they can use Terway to create the same network policies and migrate the workloads to their ACK clusters.
    -   Terway integrates Felix V3.5.2.

