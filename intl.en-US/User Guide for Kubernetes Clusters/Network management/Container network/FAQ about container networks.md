# FAQ about container networks

This topic provides answers to some commonly asked questions about container networks.

-   [How do I resolve the issue that Flannel becomes incompatible with clusters of Kubernetes 1.16 or later after I manually upgrade Flannel?](#section_z0i_qcj_rz7)
-   [How do I resolve the issue that a pod is not immediately ready for communication after it is started?](#section_g60_wqh_qtq)
-   [How do I enable a pod to access the Service that is exposed on it?](#section_w3o_3as_8lq)
-   [Which network plug-in should I choose for an ACK cluster, Terway or Flannel?](#section_ylr_ln7_pfe)
-   [How do I plan the network of a cluster?](#section_8td_x8b_qez)
-   [Can I use hostPorts to create port mappings in an ACK cluster?](#section_hlo_gm1_u0l)

## How do I solve the issue that Flannel becomes incompatible with clusters of Kubernetes 1.16 or later after I manually upgrade Flannel?

Issue:

After a cluster is upgraded to v1.16 or later, the states of nodes in the cluster change to NotReady.

Cause:

You manually upgraded Flannel without updating the Flannel configuration. As a result, kubelet cannot recognize Flannel.

Solution:

1.  Add the `cniVersion` field to the Flannel configuration.

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

It requires a period of time for the network policies to take effect. To resolve this issue, you can disable network policies.

Solution:

1.  Add the setting that disables network policies to the ConfigMap of Terway.

    ```
    kubectl edit cm -n kube-system eni-config 
    ```

    Add the following field to the ConfigMap:

    ```
    disable_network_policy: "true"
    ```

2.  If you have not upgraded Terway to the latest version, log on to the Container Service for Kubernetes \(ACK\) console and upgrade Terway.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  In the left-side navigation pane, choose **Operations** \> **Add-ons**.

    5.  On the Add-ons page, click the **Networking** tab, find the Terway section, and then click **Upgrade**.

    6.  In the **Note** message, click **OK**.

3.  Restart all of the pods that use Terway.

    ```
     kubectl delete pod -n kube-system -l app=terway-eniip
    ```


## How do I enable a pod to access the Service that is exposed on it?

Issue:

Pods are not allowed to access Services that are exposed on them. When a pod accesses the Service that is exposed on it, the performance of the Service becomes unstable or scheduling errors may occur.

Cause:

By default, Flannel does not allow loopback requests.

Solution:

-   Use a headless Service to expose and access Services. For more information, see [Headless Services](https://kubernetes.io/zh/docs/concepts/services-networking/service/#headless-services).

    **Note:** We recommend that you use this method.

-   Recreate a cluster that uses the Terway network plug-in. For more information, see [Work with Terway](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Container network/Work with Terway.md).
-   Modify the Flannel configuration, reinstall Flannel, and recreate the pod.

    **Note:** We recommend that you do not use this method because the configuration of Flannel may be overwritten if Flannel is upgraded.

    1.  Add `hairpinMode: true` to cni-config.json.

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

The following introduction describes the Flannel and Terway network plug-ins for ACK clusters.

You can select one of the following network plug-ins when you create an ACK cluster:

-   Flannel: a simple and stable Container Network Interface \(CNI\) plug-in developed by the Kubernetes community. You can use [Flannel](https://github.com/coreos/flannel) with Virtual Private Cloud \(VPC\) of Alibaba Cloud. This ensures that your clusters and containers run in a high-speed and stable network. However, Flannel provides only basic features and does not support standard Kubernetes network policies.
-   Terway: a network plug-in developed by ACK. Terway provides all the features of Flannel and allows you to attach Alibaba Cloud elastic network interfaces \(ENIs\) to containers. You can use Terway to define access control policies based on standard Kubernetes network policies for intercommunication among containers. Terway also supports bandwidth throttling on individual containers. If you do not want to use Kubernetes network policies, you can choose Flannel. In other cases, we recommend that you choose Terway. For more information about Terway, see [Work with Terway](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Container network/Work with Terway.md).

## How do I plan the network of a cluster?

When you create an ACK cluster, you must specify a VPC, vSwitches, CIDR blocks of pods, and CIDR blocks of Services. We recommend that you plan the IP address of each Elastic Compute Service \(ECS\) instance in the cluster, the CIDR blocks of Kubernetes pods, and the CIDR blocks of Services before you create an ACK cluster. For more information, see [Plan CIDR blocks for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Plan CIDR blocks for an ACK cluster.md).

## Can I use hostPorts to create port mappings in an ACK cluster?

-   No, you cannot use hostPorts to create port mappings in an ACK cluster.
-   A pod in a VPC can be accessed by other cloud resources that are deployed in the same VPC through the endpoint of the pod in the VPC. Therefore, port mapping is not required.
-   Only Services of the NodePort and LoadBalancer types can be exposed to the Internet.

