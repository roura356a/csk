# Deploy Istio on Kubernetes clusters across multiple regions {#concept_mpq_kkh_1fb .concept}

This topic describes how to deploy Istio on Kubernetes clusters created with Alibaba Cloud Container Service for Kubernetes \(ACK\) across multiple regions. Istio enables you to manage all of the traffic destined for these clusters. For example, if a Kubernetes cluster near to you fails or is overloaded, Istio deployed with such a method can seamlessly redirect the traffic destined for the cluster to another available cluster.

## Prerequisites {#section_ymx_4kh_1fb .section}

-   A Kubernetes cluster is created with ACK. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   An Alibaba Cloud account is obtained, or a RAM user that is granted with required permissions is obtained.

    **Note:** For example, you can set the cluster-admin role for a RAM user to grant corresponding permissions to the RAM user. For more information, see [Grant Kubernetes permissions to a RAM user](reseller.en-US/User Guide/Kubernetes cluster/Authorization management/Grant Kubernetes permissions to a RAM user.md#).

-   If you use a Flat network or VPN to deploy Istio on multiple Kubernetes clusters, the clusters must be located in the same VPC.
-   If you do not use a Flat network or VPN to deploy Istio on multiple Kubernetes clusters, the clusters can be located in different VPCs. In this case, the VPCs must be connected by using Cloud Enterprise Network \(CEN\) or Express Connect.
-   A network is planed to ensure that the pod CIDR blocks, service CIDR blocks, and VPC CIDR blocks of involved Kubernetes clusters do not overlap withe each other.

    **Note:** In this topic, the Flat network or VPN is used to deploy Istio on multiple Kubernetes clusters.

-   The Kubernetes cluster version is 1.10.4 or later. Any earlier version does not support Istio. Therefore, you must upgrade any earlier version to 1.10.4 or later.
-   At least three Worker nodes are set for the Kubernetes cluster.

## Procedure {#section_spk_skh_1fb .section}

**Step 1: Set the network for the target Kubernetes clusters**

-   To enable Kubernetes clusters located in the same VPC to communicate with each other, add security group rules.

    **Note:** 

    -   By default, Kubernetes clusters located in the same VPC that belong to different security groups, which in turn causes them to be unable to communicate with each other.
    -   To allow multiple security groups in the VPC to communicate with each other, you must manually configure rules to allow them to communicate with each other.
    -   For more information, see [../../../../dita-oss-bucket/SP\_2/DNECS19100344/EN-US\_TP\_9718.md\#](../../../../reseller.en-US/Security/Security groups/Add security group rules.md#).
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/395257/156230994348663_en-US.png)

-   To enable Kubernetes clusters located in the different VPCs to communicate with each other, complete the following settings:
    -   Connect the VPCs by using a CEN instance, Express Connect, or a VPN gateway.

        **Note:** 

        -   We recommend that you use a CEN instance because configuring this instance type is relatively easy and because this instance type can automatically distribute and learn routes. For more information, see [Connect VPCs](../../../../reseller.en-US/User Guide/Network connection/Connect VPCs.md#).
        -   When a Kubernetes cluster is created, the default CIDR block 192.168.0.0/16 is used to create the VPC for the Kubernetes cluster. Therefore, you must set different CIDR blocks to create the VPCs for Kubernetes clusters.
    -   Add security group rules to the security groups to which the Kubernetes clusters belong.

        **Note:** For more information, see [../../../../dita-oss-bucket/SP\_2/DNECS19100344/EN-US\_TP\_9718.md\#](../../../../reseller.en-US/Security/Security groups/Add security group rules.md#).

    -   Add a security group rule to the security group on the data plane to allow access from the CIDR block where the control plane belongs.
    -   Add a security group rule to the security group on the control plane to allow access from the CIDR block where the data plan belongs.

**Step 2: Deploy Istio through the Clusters page** 

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Clusters** \> **Clusters**.
3.  Find the target cluster. Then, in the **Action** column, choose **More** \> **Deploy Istio**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/156230994411255_en-US.png)

4.  To deploy Istio on multiple Kubernetes clusters, set the following two parameters in addition to the parameters required to [deploy Istio on a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Istio management/Deploy Istio.md#):
    -   **Enable locality based service routing**: Select this check box to route requests to the Kubernetes cluster located in the region nearest to the region where the request are sent from. By default, this check box is not selected.
    -   **Multiple clusters with a Single Control Plane**:
        -   **Disable**: Do not enable the multi-cluster mode.
        -   **Use Flat Networks or VPNs**: Use Flat networks or VPNs to enable pods of different Kubernetes cluster to communicate with each other.
        -   **No Flat Network or VPN**: Only use gateways to enable mutual communication for different Kubernetes clusters.
5.  Click **Deploy Istio**.

    The page shows the deployment progress and status in real time.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/395257/156230994448665_en-US.png)

    **Verify the result**

    1.  In the left-side navigation pane, choose **Application** \> **Pods**.
    2.  Select the target cluster and namespace to view the pods on which Istio is deployed.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/156230994411258_en-US.png)


**Step 3: Manage the ingress gateway of Istio**

Run the kubectl get service -n istio-system -l istio=ingressgateway command to obtain the Internet IP address of the ingress gateway.

**Note:** The Istio deployed in the preceding steps contains an ingress gateway that uses an Internet IP address to provide load balancing services. Applications that run in the Kubernetes clusters can be accessed through this IP address.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/395257/156230994448745_en-US.png)

**Step 4: Add the cluster where the data plane runs into Istio** 

1.  In the Kubernetes cluster where the data plane of Istio runs, run the following command to generate a kubeconfig file:

    ``` {#codeblock_lcb_m97_tak}
    kubectl create namespace istio-system
           kubectl apply -f http://istio.oss-cn-hangzhou.aliyuncs.com/istio-operator/rbac.yml
           wget http://istio.oss-cn-hangzhou.aliyuncs.com/istio-operator/generate-kubeconfig.sh
           chmod +x generate-kubeconfig.sh
           ./generate-kubeconfig.sh ${CLUSTER_NAME}
    ```

    **Note:** The $\{CLUSTER\_NAME\} must be unique in the service mesh.

2.  In the Kubernetes cluster where the control plane of Istio runs, run the following command:

    ``` {#codeblock_cya_lv8_fun}
    wget http://istio.oss-cn-hangzhou.aliyuncs.com/istio-operator/create-secret.sh
           chmod +x create-secret.sh
           ./create-secret.sh ${CLUSTER_NAME}
    ```

3.  In the Kubernetes cluster where the control plane of Istio runs, create a file $\{CLUSTER\_NAME\}.yaml and copy the following code into the file:

    ``` {#codeblock_11p_yas_qvk}
    apiVersion: istio.alibabacloud.com/v1beta1
           kind: RemoteIstio
           metadata:
           name: ${CLUSTER_NAME} 
           namespace: istio-system
           spec:
           autoInjectionNamespaces:
           - default
           dockerImage:
           region: cn-beijing
           gateways:
           k8singress: {}
           hub: registry.cn-beijing.aliyuncs.com/aliacs-app-catalog
           includeIPRanges: '*'
           proxy: {}
           security: {}
           sidecarInjector:
           enabled: true
           replicaCount: 1
    ```

4.  Run the kubectl apply -n istio-system -f $\{CLUSTER\_NAME\}.yaml command.

**Verify the result**

To check if Istio is deployed in the remote Kubernetes cluster, follow these steps:

1.  Log on to the remote Kubernetes cluster.
2.  In the left-side navigation pane, choose **Application** \> **Pod**.
3.  Select the target cluster and namespace to view the pods on which Istio is deployed.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/395257/156230994448666_en-US.png)


