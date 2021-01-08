# Use Terway

This topic describes how to use Terway in a cluster of Container Service for Kubernetes \(ACK\).

## About Terway

Terway is developed by Alibaba Cloud and serves as a network plug-in of ACK. Terway is fully compatible with Flannel and supports the following operations:

-   Assign elastic network interfaces \(ENIs\) of Alibaba Cloud and secondary IP addresses to containers.
-   Define access policies based on standard Kubernetes network policies for intercommunication among containers. Terway is compatible with Calico network policies.

![Terway network plug-in](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0545359951/p32414.png)

In a cluster that has Terway installed, each pod has a separate network stack and is assigned a separate IP address. Pods on the same Elastic Compute Service \(ECS\) instance communicate with each other by forwarding packets inside the ECS instance. Pods on different ECS instances communicate with each other through an ENI in the virtual private cloud \(VPC\) where the ECS instance is deployed. This improves communication efficiency because you do not need to encapsulate packets by using tunneling technologies, such as Virtual Extensible Local Area Network \(VXLAN\).

## Considerations

-   To use Terway, we recommend that you use ECS instances of higher specifications and later types, such as ECS instances that belong to the g5 or g6 instance family with at least eight CPU cores.
-   The maximum number of pods that each node supports is based on the number of ENIs on the node.

    -   Maximum number of pods supported by each shared ENI = \(Number of ENIs supported by each ECS instance - 1\) × Number of private IP addresses supported by each ENI
    -   Maximum number of pods supported by each dedicated ENI = Number of ENIs supported by each ECS instance -1
    **Note:** You can view the number of ENIs supported by each ECS instance in the Instance Type section when you create or expand a cluster.


## Work with Terway

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  In the upper-right corner of the Clusters page, click **Create Kubernetes Cluster**.

4.  On the Select Cluster Template page, click **Standard Managed Cluster**.

    **Note:** A standard managed Kubernetes cluster is created in this topic. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

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
-   If you clear Support for NetworkPolicy, you cannot use Kubernetes network policies to control communication among pods. This prevents Kubernetes network policies from posing heavy loads on the Kubernetes API server.

## Increase the number of vSwitch IP addresses for an ACK cluster that uses Terway

If the number of vSwitch IP addresses is insufficient for an ACK cluster that uses Terway, you must create vSwitches in the VPC where the cluster is deployed. The vSwitch IP addresses may be insufficient in the following cases:

-   A pod cannot be created and remains in the ContainerCreating state. In this case, run the following command to query Terway logs on the node where the pod is deployed:

    ```
    kubectl logs --tail=100 -f terway-eniip-zwjwx -n kube-system -c terway
    ```

    If an error message similar to the following content is returned, it indicates that the vSwitch that is specified by Terway has insufficient IP addresses for the pod to be created.

    ```
    time="2020-03-17T07:03:40Z" level=warning msg="Assign private ip address failed: Aliyun API Error: RequestId: 2095E971-E473-4BA0-853F-0C41CF52651D Status Code: 403 Code: InvalidVSwitchId.IpNotEnough Message: The specified VSwitch \"vsw-AAA\" has not enough IpAddress., retrying"
    ```

-   Log on to the [VPC console](https://vpc.console.aliyun.com/). In the left-side navigation pane, click **VSwitches**. On the VSwitches page, find the used vSwitch and you can find that 0 appears in the **Number of Available Private IPs** column.

You can add a vSwitch to provide more IP addresses for the cluster. Perform the following steps:

1.  Log on to the [VPC console](https://vpc.console.aliyun.com/) and create a vSwitch. You must create the vSwitch in the same region as the preceding vSwitch that has insufficient IP addresses. For more information about how to create a vSwitch, see [Create a VSwitch](/intl.en-US/VPCs and VSwitches/VSwitch management/Create a VSwitch.md).

    **Note:** To provide sufficient IP addresses for an increasing number of pods in the ACK cluster, we recommend that you specify up to 19 bits for the length of the network identification when you create the vSwitch. This way, the created vSwitch provides at least 8,192 IP addresses.

2.  Run the following command to add the newly created vSwitch to the ConfigMap file of Terway:

    ```
    kubectl edit cm eni-config -n kube-system
    ```

    The following code block shows how to configure the ConfigMap:

    ```
    eni_conf: |
    {
    "version": "1",
    "max_pool_size": 25,
    "min_pool_size": 10,
    "vswitches": {"cn-shanghai-f":["vsw-AAA", "vsw-BBB"]},
    "service_cidr": "172.21.0.0/20",
    "security_group": "sg-CCC"
    }
    ```

    In this example, `vsw-BBB` is added to the value that is related to `VSwitches`. `vsw-AAA` represents the existing vSwitch that has insufficient IP addresses.

3.  If the installed Terway is not the latest version, perform Step 3 to Step 6 to upgrade Terway. If the installed Terway is the latest version, go to Step 7.
4.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

5.  In the left-side navigation pane, click **Clusters**.

6.  On the Clusters page, find the cluster that you want to manage and click **Details** in the **Actions** column.

7.  In the left-side navigation pane, choose **Operations** \> **Add-ons**. Perform the following steps to upgrade the installed Terway to the latest version.

    1.  On the Add-ons page, click the **Networking** tab.

    2.  On the Networking tab, find the Terway plug-in and click **Upgrade**.

8.  Run the following command to delete all Terway pods. ACK automatically recreates pods for Terway.

    If one ENI is shared among multiple pods, run the following command:

    ```
    kubectl delete -n kube-system pod -l app=terway-eniip
    ```

    If one ENI is exclusive to one pod, run the following command:

    ```
    kubectl delete -n kube-system pod -l app=terway-eni
    ```

    Run the following command to check whether all Terway pods are recreated:

    ```
    kubectl get pod -n kube-system  | grep terway
    ```

9.  After the preceding steps are completed, you can also create a pod to check whether the newly create pod can be assigned an IP address from the newly created vSwitch.


If exceptions occur in the preceding steps, [Submit a ticket]()

## Flannel and Terway

When you create an ACK cluster, you can select Terway or Flannel as the network plug-in.

-   [Flannel](https://github.com/coreos/flannel): a simple and stable Container Network Interface \(CNI\) plug-in that is developed by the Kubernetes community. You can use Flannel with VPC of Alibaba Cloud. This allows your clusters and containers to run in a high-performance and stable network. However, Flannel provides only basic features. It does not support standard Kubernetes network policies.
-   Terway: a network plug-in that is developed by ACK. Terway allows you to assign ENIs to containers and define access policies based on standard Kubernetes network policies for intercommunication among containers. Terway also supports bandwidth throttling on individual containers. If you do not need to use network policies, you can select Flannel as the network plug-in. Otherwise, we recommend that you select Terway.

    **Note:**

    -   Terway integrates the Felix component of Calico to enforce network policies. Therefore, you can use Terway to create network policies as you do with Calico. For users that use Calico in self-managed clusters, they can use Terway to create the same network policies and migrate the workloads to their ACK clusters.
    -   Terway integrates Felix V3.5.2.

