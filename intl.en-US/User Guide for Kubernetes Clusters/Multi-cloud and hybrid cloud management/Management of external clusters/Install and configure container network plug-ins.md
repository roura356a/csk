---
keyword: [container network plug-in, Terway network]
---

# Install and configure container network plug-ins

The container network plug-ins used in a hybrid cluster consist of two parts: the network plug-ins that run in the data center and the network plug-ins that run on cloud-computing nodes. This topic describes how to install and configure the container network plug-ins in a hybrid cluster.

-   In [Scenario 2: The data center uses a BGP network for container networking](#section_jo1_b9e_q7v) and [Scenario 3: The data center uses the host network for container networking](#section_dpw_bab_n97), Terway parameters are set when you create the cluster registration proxy.

-   IPVLAN is set based on business requirements.
-   The number of pod vSwitches is set to an appropriate value.
-   Service CIDR is set.
For more information, see [t380912.dita\#task\_skz\_qwk\_qfb](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of external clusters/Create a cluster registration proxy and register an on-premises cluster.md).

-   The operations before [Step 4](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of external clusters/Create a hybrid cluster.md) in the Create a hybrid cluster topic are completed.

## Scenario 1: The data center uses an overlay network for container networking

If the data center uses an overlay network for container networking, cloud-computing nodes can also use this network mode. You need only to make sure that the cloud-computing nodes can pull the container image used by the DaemonSet of the container network plug-in.

The following overlay network modes are frequently used:

-   Flannel VXLAN
-   Calico IPIP
-   Cilium VXLAN

## Scenario 2: The data center uses a BGP network for container networking

If the data center uses a Border Gateway Protocol \(BGP\) network for container networking, you must use the Terway network plug-in on cloud-computing nodes. For more information about connecting on-premises networks and the cloud, see [Configure BGP](Configure BGPt21437.dita#concept_ljj_4vx_dfb).

In this scenario, make sure that the following conditions are met:

-   The DaemonSet of the on-premises container network plug-in, such as BGP route reflector in Calico, must not be scheduled to cloud-computing nodes.
-   The DaemonSet of the Terway network plug-in must not be scheduled to on-premises computing nodes.

Each computing node that is added from a node pool through the cluster registration proxy has the `alibabacloud.com/external=true` label. You can use this label to differentiate cloud nodes from on-premises nodes.

For example, you can create `node affinity` rules to make sure that the DaemonSet of the on-premises Calico network plug-in is not scheduled to nodes that have the `alibabacloud.com/external=true` label. You can use the same method to ensure that other on-premises workloads are not scheduled to cloud-computing nodes. Run the following command to update the Calico network plug-in:

```
cat <<EOF > calico-ds.pactch
spec:
  template:
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: alibabacloud.com/external
                operator: NotIn
                values:
                - "true"
EOF
kubectl -n kube-system patch ds calico-node -p "$(cat calico-ds.pactch)"
```

By default, the DaemonSet of the Terway network plug-in is scheduled only to nodes that have the `alibabacloud.com/external=true` label.

## Scenario 3: The data center uses the host network for container networking

If the data center uses the host network for container networking, you need only to make sure that the DaemonSet of the Terway network plug-in is not scheduled to on-premises computing nodes. By default, the DaemonSet of the Terway network plug-in is scheduled only to nodes that have the `alibabacloud.com/external=true` label.

## Install and configure the Terway network plug-in

In [Scenario 1](#section_jo1_b9e_q7v) and [Scenario 2](#section_dpw_bab_n97), you must install and configure the Terway network plug-in on the cloud-computing nodes of the hybrid cluster.

**Step 1: Install the Terway network plug-in**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Add-ons**.

5.  On the Add-ons page, click the **Networking** tab. Select the **terway-eniip** component and then click **Install**.

    ![terway-eniip installation](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/2511766161/p247541.png)

    You can use kubeconfig to connect to the registered cluster and query the DaemonSet of the Terway network plug-in. Before cloud-computing nodes are added to the hybrid cluster, the DaemonSet will not be scheduled to on-premises nodes.

    Run the following command to query the Terway network:

    ```
    kubectl -nkube-system get ds |grep terway
    ```

    Expected output:

    ```
    terway-eniip   0         0         0       0            0           alibabacloud.com/external=true      16s
    ```


**Step 2: Configure the Terway network plug-in**

In registered clusters, all add-ons use custom AccessKey pairs to obtain the required permissions to access cloud resources.

1.  Create a Resource Access Management \(RAM\) user and attach the following RAM permission policies to the RAM user.

    For more information, see [Procedure](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create a custom RAM policy.md).

    ```
    {
        "Version": "1",
        "Statement": [
            {
                "Action": [
                    "ecs:CreateNetworkInterface",
                    "ecs:DescribeNetworkInterfaces",
                    "ecs:AttachNetworkInterface",
                    "ecs:DetachNetworkInterface",
                    "ecs:DeleteNetworkInterface",
                    "ecs:DescribeInstanceAttribute",
                    "ecs:AssignPrivateIpAddresses",
                    "ecs:UnassignPrivateIpAddresses",
                    "ecs:DescribeInstances",
                    "ecs:ModifyNetworkInterfaceAttribute"
                ],
                "Resource": [
                    "*"
                ],
                "Effect": "Allow"
            },
            {
                "Action": [
                    "vpc:DescribeVSwitches"
                ],
                "Resource": [
                    "*"
                ],
                "Effect": "Allow"
            }
        ]
    }
    ```

2.  Run the following command to edit the eni-config ConfigMap and configure `eni_conf.access_key` and `eni_conf.access_secret`:

    ```
    kubectl -n kube-system edit cm eni-config
    ```

    The following eni-config ConfigMap is provided as an example:

    ```
    kind: ConfigMap
    apiVersion: v1
    metadata:  
     name: eni-config  
     namespace: kube-system
    data:  
     eni_conf: |    
      {      
       "version": "1",      
       "max_pool_size": 5,      
       "min_pool_size": 0,      
       "vswitches": {{.PodVswitchId}},      
       "eni_tags": {"ack.aliyun.com":"{{.ClusterID}}"},      
       "service_cidr": "{{.ServiceCIDR}}",      
       "security_group": "{{.SecurityGroupId}}",      
       "access_key": "",      
       "access_secret": "",      
       "vswitch_selection_policy": "ordered"    
      }  
     10-terway.conf: |    
      {      
       "cniVersion": "0.3.0",      
       "name": "terway",      
       "type": "terway"    
      }
    ```


