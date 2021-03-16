---
keyword: [associate an EIP, Terway mode, dedicated public IP address of a pod]
---

# Associate an EIP with a pod

In most cases, a pod uses a private IP address. In a Container Service for Kubernetes \(ACK\) cluster that uses Terway, a pod may require a dedicated public IP address in some cases. This topic describes how to associate an elastic IP address \(EIP\) with a pod in a cluster that uses Terway.

In most cases, to enable a pod to access the Internet, the IP address of the pod is converted to an EIP that is associated with the host or the Network Address Translation \(NAT\) gateway of the virtual private cloud \(VPC\) based on Source Network Address Translation \(SNAT\) rules. The inbound Internet traffic to the pod is routed through the related LoadBalancer type Services. A pod may require a dedicated public IP address in the following scenarios.

-   The pod exposes random ports to the Internet. For example, game servers and audio conferences that use the User Datagram Protocol \(UDP\). For example, if the Real Time Streaming Protocol \(RTSP\) is used, different ports are used for different clients.
-   The pod competes with other pods for a public IP address to access the Internet. In this case, the pod requires a dedicated public IP address to access the Internet.

For more information about how to use the Terway network plug-in in an ACK cluster, see [Work with Terway](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Work with Terway.md).

## Step 1: Upgrade Terway to the latest version

Upgrade Terway in your ACK cluster to a version that supports EIP. For more information about how to upgrade Terway, see [Manage system components](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Upgrade cluster/Manage system components.md).

**Note:** Terway `V1.0.10.280-gdc2cb6c-aliyun` and later support EIP. We recommend that you upgrade Terway to the latest version.

## Step 2: Configure and grant permissions to manage EIPs

You must configure and grant EIP-related permissions to the cluster that uses Terway.

1.  Grant EIP-related permissions to the Resource Access Management \(RAM\) role of the cluster that uses Terway.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com)[ACK console](https://partners-intl.console.aliyun.com/#/cs).

    2.  In the left-side navigation pane, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  Click the **Cluster Resources** tab and click the hyperlink on the right side of **Worker RAM Role**.

    5.  Click the **Permissions** tab and click the name of the policy. The details page of the policy appears.

        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8285268951/p11193.png)

    6.  Click **Modify Policy Document**. In the Modify Policy Document panel that appears on the right side of the page, add the following content to the `Action` field in the **Policy Document** section. Click **OK**.

        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8285268951/p11195.png)

        ```
        "vpc:DescribeVSwitches",
        "vpc:AllocateEipAddress",
        "vpc:DescribeEipAddresses",
        "vpc:AssociateEipAddress",
        "vpc:UnassociateEipAddress",
        "vpc:ReleaseEipAddress"
        ```

        **Note:** Add a comma \(,\) to the end of each line in the `Action` field.

    Grant the following EIP-related permissions to the AliyunCSManagedNetworkRole RAM role in the RAM console.

    ```
    "vpc:DescribeVSwitches",
    "vpc:AllocateEipAddress",
    "vpc:DescribeEipAddresses",
    "vpc:AssociateEipAddress",
    "vpc:UnassociateEipAddress",
    "vpc:ReleaseEipAddress"
    ```

    1.  On the [RAM role AliyunCSManagedNetworkRole](https://ram.console.aliyun.com/roles/AliyunCSManagedNetworkRole)[RAM role AliyunCSManagedNetworkRole](https://partners-intl.aliyun.com/roles/AliyunCSManagedNetworkRole) page, find the policy and click **Add Permissions** on the right side of the page.

    2.  In the Add Permissions panel, click **+ Create Policy** in the **Select Policy** section to create a custom policy.

        For more information about how to create a custom policy, see [Create a custom policy](/intl.en-US/Policy Management/Custom policies/Create a custom policy.md).

    3.  Add the following policy content to the custom policy.

        For more information about how to modify a custom policy, see [Modify a custom policy](/intl.en-US/Policy Management/Custom policies/Modify a custom policy.md).

        ```
        "vpc:DescribeVSwitches",
        "vpc:AllocateEipAddress",
        "vpc:DescribeEipAddresses",
        "vpc:AssociateEipAddress",
        "vpc:UnassociateEipAddress",
        "vpc:ReleaseEipAddress"
        ```

2.  Configure Terway to support EIP.

    1.  Run the following command to modify the eni\_conf ConfigMap of the Terway configurations:

        ```
        kubectl edit cm eni-config -n kube-system
        ```

    2.  Add the following content to the eni\_conf ConfigMap:

        ```
        "enable_eip_pool": "true"
        ```

        **Note:** If you want to disassociate the existing EIP when you associate a new one, add "allow\_eip\_rob": "true" to the eni\_conf ConfigMap.

    3.  Run the following command to redeploy Terway:

        ```
        kubectl delete pod -n kube-system -l app=terway-eniip
        ```


## Step 3: Add annotations to create and associate an EIP with a pod

You can add annotations to the configurations of a pod to create or associate an EIP with the pod. Add annotations as described in the following scenarios:

-   **Automatically create and associate an EIP with a pod**
    -   Add the following annotation to create and associate an EIP with a pod:

        ```
        k8s.aliyun.com/pod-with-eip: "true"
        ```

        The following YAML file is provided as an example.

        ```
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: nginx-deployment-basic
          labels:
            app: nginx
        spec:
          replicas: 2
          selector:
            matchLabels:
              app: nginx
          template:
            metadata:
              annotations:
                k8s.aliyun.com/pod-with-eip: "true" # Specifies that an EIP is automatically created and associated with the NGINX containers.
              labels:
                app: nginx
            spec:
              containers:
              - name: nginx
                image: nginx
        ```

    -   Add the following annotation to specify the bandwidth for the EIP. The default EIP bandwidth is 5 Mbit/s.

        ```
        k8s.aliyun.com/eip-bandwidth: 5M
        ```

-   **Specify an existing EIP**
    -   Add the following annotation to specify the ID of an existing EIP and associate it with a pod:

        ```
        k8s.aliyun.com/pod-eip-instanceid: "<youreipInstanceId>"
        ```

        **Note:**

        -   An EIP cannot be associated with multiple pods. Therefore, you cannot use this annotation for pods of Deployments and StatefulSets.
        -   By default, if an EIP is already associated with a pod, you fail to create a new EIP for the pod. If you want to disassociate the existing EIP and create a new one, add the "allow\_eip\_rob": "true" setting to the eni\_conf ConfigMap as described in the preceding [step](#substep_8ko_vpt_wxa).
        -   You can specify an existing EIP only when the application runs one pod replica. For example, a Deployment that runs only one pod replica.

## Verify the configurations

When the pod changes to the Running state, you can check the value of k8s.aliyun.com/allocated-eipAddress in the pod annotations. The value is the associated EIP. You can access the pod through the EIP.

**Related topics**  


[Plan CIDR blocks for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Plan CIDR blocks for an ACK cluster.md)

[Work with Terway](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Work with Terway.md)

