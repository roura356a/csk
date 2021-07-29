# FAQ about container networks

This topic provides answers to some frequently asked questions about container networks.

-   [How do I resolve the issue that the cluster installed with the Terway network plug-in cannot access the Internet after I create a new vSwitch for the cluster?](#section_7hi_dnx_552)
-   [How do I resolve the issue that Flannel becomes incompatible with clusters of Kubernetes 1.16 or later after I manually upgrade Flannel?](#section_z0i_qcj_rz7)
-   [How do I resolve the issue that a pod is not immediately ready for communication after it is started?](#section_g60_wqh_qtq)
-   [How do I enable a pod to access a Service that is exposed on the pod?](#section_w3o_3as_8lq)
-   [Which network plug-in should I choose for an ACK cluster, Terway or Flannel?](#section_ylr_ln7_pfe)
-   [How do I plan the network of a cluster?](#section_8td_x8b_qez)
-   [Can I use hostPorts to create port mappings in an ACK cluster?](#section_hlo_gm1_u0l)
-   [Can I configure multiple route tables for the VPC where my cluster is deployed?](#section_9lf_6ud_9zg)
-   [How do I check the network type and vSwitches of a cluster?](#section_bgi_kgm_etw)
-   [How do I check the cloud resources used in an ACK cluster?](#section_8b1_slo_xiu)
-   [How do I resolve network errors of pods in the cluster?](~~142373~~)
-   [How do I obtain the public IP address of an application in the cluster?](~~142274~~)
-   [How do I resolve network errors that occur when the Terway network plug-in is used in exclusive ENI mode?](~~147426~~)
-   [How do I troubleshoot cluster connection issues?](~~149275~~)
-   [How do I resolve the issue that the number of IP addresses provided by the vSwitch is insufficient when the Terway network plug-in is used?](~~189784~~)
-   [How do I resolve the issue that the cluster cannot connect to the public IP address of the SLB instance that is associated with the LoadBalancer Service?](~~171437~~)

## How do I resolve the issue that the cluster installed with the Terway network plug-in cannot access the Internet after I create a new vSwitch for the cluster?

Symptom: In the Terway network, after a new vSwitch is created to provide more IP addresses for pods, the cluster cannot access the Internet.

Cause: The new vSwitch that assigns IP addresses to pods does not have access to the Internet.

Solution: You can create a NAT gateway and configure SNAT rules to enable the new vSwitch to access the Internet. For more information, see [Enable an existing ACK cluster to access the Internet by using SNAT](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Enable an existing ACK cluster to access the Internet by using SNAT.md).

## How do I resolve the issue that Flannel becomes incompatible with clusters of Kubernetes 1.16 or later after I manually upgrade Flannel?

Issue:

After the Kubernetes version of a cluster is upgraded to 1.16 or later, the nodes in the cluster change to the NotReady state.

Cause:

You manually upgraded Flannel but did not update the Flannel configuration. As a result, kubelet cannot recognize Flannel.

Solution:

1.  Modify the Flannel configuration file to add the `cniVersion` field.

    ```
    kubectl edit cm kube-flannel-cfg -n kube-system 
    ```

    Add the `cniVersion` field to the configuration.

    ```
    "name": "cb0",      
    "cniVersion":"0.3.0",
    "type": "flannel",
    ```

2.  Restart Flannel.

    ```
    kubectl delete pod -n kube-system -l app=flannel
    ```


## How do I resolve the issue that a pod is not immediately ready for communication after it is started?

Issue:

After a pod is started, you must wait for a period of time before the pod is ready for communication.

Cause:

The network policies require a period of time to take effect. To resolve this issue, you can disable network policies.

Solution:

1.  Add the setting that is used to disable network policies to the ConfigMap of Terway.

    ```
    kubectl edit cm -n kube-system eni-config 
    ```

    Add the following field to the ConfigMap:

    ```
    disable_network_policy: "true"
    ```

2.  If Terway is not upgraded to the latest version, log on to the Container Service for Kubernetes \(ACK\) console and upgrade Terway.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  In the left-side navigation pane, choose **Operations** \> **Add-ons**.

    5.  On the Add-ons page, click the **Networking** tab, find the Terway section, and then click **Upgrade**.

    6.  In the **Note** message, click **OK**.

3.  Restart all pods of Terway.

    ```
     kubectl delete pod -n kube-system -l app=terway-eniip
    ```


## How do I enable a pod to access a Service that is exposed on the pod?

Issue:

Pods are not allowed to access Services that are exposed on them. If a pod accesses the Service that is exposed on the pod, the performance of the Service becomes unstable or scheduling errors may occur.

Cause:

By default, Flannel does not allow loopback requests.

Solution:

-   Use a headless Service to expose and access Services. For more information, see [Headless Services](https://kubernetes.io/zh/docs/concepts/services-networking/service/#headless-services).

    **Note:** We recommend that you use this method.

-   Recreate a cluster that uses the Terway network plug-in. For more information, see [Use the Terway plug-in](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Use the Terway plug-in.md).
-   Modify the Flannel configuration, reinstall Flannel, and then recreate the pod.

    **Note:** We recommend that you do not use this method because the Flannel configuration may be overwritten when you upgrade Flannel.

    1.  Add the `hairpinMode: true` field to cni-config.json.

        ```
        kubectl edit cm kube-flannel-cfg -n kube-system
        ```

        Add the following field to the configuration:

        ```
        hairpinMode: true
        ```

    2.  Restart Flannel.

        ```
        kubectl delete pod -n kube-system -l app=flannel   
        ```

    3.  Delete and recreate the pod.

## Which network plug-in should I choose for an ACK cluster, Terway or Flannel?

The following section describes the Flannel and Terway network plug-ins for ACK clusters.

You can select one of the following network plug-ins when you create an ACK cluster:

-   Flannel: a simple and stable Container Network Interface \(CNI\) plug-in developed by the Kubernetes community. You can use [Flannel](https://github.com/coreos/flannel) with Virtual Private Cloud \(VPC\) of Alibaba Cloud. This ensures that your clusters and containers run in a high-speed and stable network. However, Flannel provides only basic features and does not support standard Kubernetes network policies.
-   Terway: a network plug-in developed by ACK. Terway provides all the features of Flannel and allows you to attach Alibaba Cloud elastic network interfaces \(ENIs\) to containers. You can use Terway to configure access control policies for containers based on standard Kubernetes network policies. Terway also supports bandwidth throttling on individual containers. If you do not want to use Kubernetes network policies, you can choose Flannel. In other cases, we recommend that you choose Terway. For more information about Terway, see [Use the Terway plug-in](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Use the Terway plug-in.md).

## How do I plan the network of a cluster?

When you create an ACK cluster, you must specify a VPC, vSwitches, CIDR blocks of pods, and CIDR blocks of Services. We recommend that you plan the IP address of each Elastic Compute Service \(ECS\) instance in the cluster, the CIDR blocks of Kubernetes pods, and the CIDR blocks of Services before you create an ACK cluster. For more information, see [Plan CIDR blocks for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Plan CIDR blocks for an ACK cluster.md).

## Can I use hostPorts to create port mappings in an ACK cluster?

-   No, you cannot use hostPorts to create port mappings in an ACK cluster.
-   A pod in a VPC can be accessed by other cloud resources that are deployed in the same VPC through the endpoint of the pod in the VPC. Therefore, port mapping is not required.
-   To expose Services to the Internet, you can create Services of the NodePort or LoadBalancer type.

## Can I configure multiple route tables for the VPC where my cluster is deployed?

Only dedicated ACK clusters allow you to configure multiple route tables for the VPC. For more information, see [Configure multiple route tables for a VPC](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Configure multiple route tables for a VPC.md). To configure multiple route tables for the VPC where a managed ACK cluster is deployed, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

## How do I check the network type and vSwitches of a cluster?

ACK supports two types of container network: Flannel and Terway.

**To check the network type of the cluster, perform the following steps:**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).
2.  In the left-side navigation pane, click **Clusters**.
3.  On the Clusters page, find the cluster that you want to manage and click the name or click **Details** in the Actions column. The details page of the cluster appears.
4.  On the **Basic Information** tab, check the value of **Network Plug-in** in the **Cluster Information** section.
    -   If the value of **Network Plug-in** is **terway-eniip**, it indicates that the Terway network is used.
    -   If the value of **Network Plug-in** is **Flannel**, it indicates that the Flannel network is used.

**To check the vSwitches used in the container network, perform the following steps:**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).
2.  In the left-side navigation pane, click **Clusters**.
3.  On the Clusters page, find the cluster that you want to manage and click the name or click **Details** in the Actions column. The details page of the cluster appears.
4.  On the **Cluster Resources** tab, check the vSwitches used in the network.
    -   If the cluster uses the Terway network, you can view **Node Vswitch** and **Pod Vswitch** on the **Cluster Resources** tab.
    -   If the cluster uses the Flannel network, you can view **Node Vswitch** on the **Cluster Resources** tab.

## How do I check the cloud resources used in an ACK cluster?

You can perform the following steps to check the cloud resources used in ACK clusters, such as vSwitches, VPCs, and worker RAM roles:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).
2.  In the left-side navigation pane, click **Clusters**.
3.  On the Clusters page, find the cluster that you want to manage and click the name or click **Details** in the Actions column. The details page of the cluster appears.
4.  On the **Cluster Resources** tab, view the information about the cloud resources used in the cluster.

