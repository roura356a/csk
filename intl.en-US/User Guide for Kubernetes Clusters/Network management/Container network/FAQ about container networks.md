# FAQ about container networks

This topic provides answers to some commonly asked questions about container networks.

## How do I solve the issue that Flannel becomes incompatible with clusters of Kubernetes 1.16 or later after I manually upgrade Flannel?

Symptom:

After a cluster is upgraded to v1.16 or later, the states of nodes in the cluster change to NotReady.

Cause:

You manually upgraded Flannel without updating the Flannel configuration. As a result, Kubelet cannot recognize Flannel.

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

Symptom:

After a pod is started, the pod must wait a period of time before it is ready for communication.

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

    2.  In the left-side navigation pane, click **Clusters**.

    3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  In the left-side navigation pane, choose **Operations** \> **Add-ons**.

    5.  On the Add-ons page, click the **Networking** tab, find the Terway section, and then click **Upgrade**.

    6.  In the **Note** message, click **OK**.

3.  Restart all of the pods that use Terway.

    ```
     kubectl delete pod -n kube-system -l app=terway-eniip
    ```


## How do I enable a pod to access the Service that is exposed on it?

Symptom:

Pods are not allowed to access Services that are exposed on them. When a pod accesses the Service that is exposed on it, the performance of the Service becomes unstable or scheduling errors may occur.

Cause:

By default, Flannel does not allow loopback requests.

Solution:

-   Use a headless Service to expose and access Services. For more information, see [Headless Services](https://kubernetes.io/zh/docs/concepts/services-networking/service/#headless-services).

    **Note:** We recommend that you use this method.

-   Recreate a cluster that uses the Terway network plug-in. For more information, see [Use Terway](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Container network/Use Terway.md).
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
-   Terway: a network plug-in developed by ACK. Terway provides all the features of Flannel and allows you to assign Alibaba Cloud elastic network interfaces \(ENIs\) to containers. You can use Terway to define access control policies based on standard Kubernetes network policies for intercommunication among containers. Terway also supports bandwidth throttling on individual containers. If you do not need to use Kubernetes network policies, you can choose Flannel. In other cases, we recommend that you choose Terway. For more information about Terway, see [Use Terway](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Container network/Use Terway.md).

## How do I plan the network of a cluster?

When you create an ACK cluster, you must specify a VPC, vSwitches, CIDR blocks of pods, and CIDR blocks of Services. We recommend that you decide the IP address of each Elastic Compute Service \(ECS\) instance in the cluster, CIDR blocks of pods, and CIDR blocks of Services before you create an ACK cluster. For more information, see [Assign CIDR blocks to resources in a Kubernetes cluster under a VPC](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Assign CIDR blocks to resources in a Kubernetes cluster under a VPC.md).

