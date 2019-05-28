# Deploy a virtual node {#concept_264451 .concept}

This topic describes how to deploy a virtual node for a Kubernetes cluster by using the virtual node addon.

## Background information {#section_3ic_pu5_oqy .section}

Virtual nodes are implemented by using Virtual Kubelet. Virtual nodes connect Kubernetes with Alibaba Cloud Elastic Container Instances \(ECI\), and provide Kubernetes clusters with a high level of elasticity. For more information about Virtual Kubelet, see [How does Virtual Kubelet work?](https://virtual-kubelet.io/docs/architecture/) 

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/219715/155902187647947_en-US.png)

Virtual nodes can help to enhance the elasticity of Kubernetes clusters. Specifically, virtual nodes based on Alibaba Cloud ECI can enhance the elasticity of clusters because they support GPU container instances, EIP addresses, and container instances of high specifications. Given the expansion capability provided by virtual nodes, you can easily manage multiple workloads for computing in a Kubernetes cluster.

In a cluster that contains virtual nodes, the pod in a physical node communicates with the ECI pod in the corresponding virtual node.

**Note:** 

-   The ECI pod in a virtual node is charged according to the specific amount of resources that you use. For information about ECI billing rules, see [Billing overview](https://www.alibabacloud.com/help/doc-detail/89142.htm?spm=a2c63.l28256.b99.8.74993edar7Hvt0).
-   ECS instances of the specifications range of 0.25 vCPU to 64 vCPU are supported. For more information, see [Limits](https://www.alibabacloud.com/help/doc-detail/89138.htm?spm=a2c63.p38356.b99.6.49d3defcv55ebG).

## Prerequisites {#section_pao_a84_oyd .section}

A managed Kubernetes cluster is created. For more information, see [Create a managed Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a managed Kubernetes cluster.md#).

## Create a virtual node for a cluster Work node {#section_yt7_kmd_6d5 .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
2.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
3.  In the left-side navigation pane under Container Service-Kubernetes, choose **Store** \> **App Catalog**. Then, click **ack-virtual-node**.
4.  Click the **Values** tab, and then set these parameters.
    -   ECI\_VSWITCH: Set the VSwitch ID.

        To obtain the ID of the VSwitch that is associated with a Worker node, follow these steps:

        1.  In the left-side navigation pane under Container Service-Kubernetes, choose **Clusters** \> **Nodes**.
        2.  Select the target cluster, and then click a Worker node.
        In the **Configuration Information** area, the VSwitch ID is displayed.

    -   ECI\_SECURITY\_GROUP: Set the security group ID.

        To obtain the ID of the security group that is associated with a Worker node, follow these steps:

        1.  In the left-side navigation pane under Container Service-Kubernetes, choose **Clusters** \> **Nodes**.
        2.  Select the target cluster, and then click a Worker node.
        3.  In the left-side navigation pane, click **Security Groups**.
        On the Security Groups page, the security group ID is displayed.

    -   ECI\_ACCESS\_KEY: Set the AccessKey.
    -   ECI\_SECRET\_KEY: Set the SecretKey.
5.  In the **Deploy** area on the right of the page, select the target cluster, and then click **DEPLOY**.

    **Note:** By default, the namespace is set to kube-system, and the release name is set to ack-virtual-node.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/219715/155902187647943_en-US.png)

6.  In the left-side navigation pane under Container Service-Kubernetes, choose **Clusters** \> **Nodes** to verify that a node named virtual-kubelet is displayed.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/219715/155902187647944_en-US.png)

    You can use kubectl commands to view cluster nodes, and the status of Helm deployment. You can also use Helm to upgrade and manage the deployed ack-virtual-node. For more information, see [Use kubectl commands in Cloud Shell to manage a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Use kubectl commands in Cloud Shell to manage a Kubernetes cluster.md#).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/219715/155902187647934_en-US.png)


## Create a pod in the virtual node {#section_ude_91e_zpe .section}

If a Kubernetes cluster contains a virtual node, you can create a pod in the virtual node. Then, the virtual kubelet will create a corresponding ECI pod. You can use one of the following three methods to create a pod for the virtual node:

-   Set `nodeSelector` and `tolerations` to create a pod.
    1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
    2.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
    3.  In the left-side navigation pane under Container Service-Kubernetes, choose **Applications** \> **Deployments**. Then, in the upper-right corner, click **Create by Template**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/219715/155902187647932_en-US.png)

    4.  Select the target cluster and namespace, select an example template or customize a template, and then click **DEPLOY**.

        You can use the following template to customize a pod:

        ``` {#codeblock_9gq_lu5_8mt}
        apiVersion: v1
        kind: Pod
        metadata:
          name: nginx
        spec:
          containers:
          - image: nginx
            imagePullPolicy: Always
            name: nginx
          nodeSelector:
            type: virtual-kubelet
          tolerations:
          - key: virtual-kubelet.io/provider
            operator: Exists
        ```

-   Set `nodeName` to create a pod.
    1.  In the left-side navigation pane under Container Service-Kubernetes, choose **Applications** \> **Deployments**. Then, in the upper-right corner, click **Create by Template**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/219715/155902187647932_en-US.png)

    2.  Select the target cluster and namespace, select an example template or customize a template, and then click **DEPLOY**.

        You can use the following template to customize a pod:

        ``` {#codeblock_hyh_066_7dg}
        apiVersion: v1
        kind: Pod
        metadata:
          name: nginx
        spec:
          containers:
          - image: nginx
            imagePullPolicy: Always
            name: nginx
          nodeName: virtual-kubelet
        ```

-   Set a namespace tag to create a pod.
    1.  Use kubectl on Cloud Shell to connect to the target Kubernetes cluster. For more information, see [Use kubectl commands in Cloud Shell to manage a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Use kubectl commands in Cloud Shell to manage a Kubernetes cluster.md#).
    2.  Run the following commands to create a pod:

        ``` {#codeblock_y9o_npq_tfi}
        kubectl create ns vk
        kubectl label namespace vk virtual-node-affinity-injection=enabled
        kubectl -n vk run nginx --image nginx
        ```

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/219715/155902187647933_en-US.png)


In the left-side navigation pane under Container Service-Kubernetes, choose **Applications** \> **Pods** to verify that the pod is created.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/219715/155902187647945_en-US.png)

