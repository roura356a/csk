---
keyword: [enhanced edge node pool, ACK@Edge, ACK@Edge]
---

# Create an enhanced edge node pool \(public preview\)

Enhanced cloud-edge networking is developed based on the Software Defined Network \(SDN\) solution of ACK@Edge. An edge node can connect to a Cloud Connect Network \(CCN\) instance through the nearest access point in the global transmission network of Alibaba Cloud. The CCN instance can communicate with virtual private clouds \(VPCs\) that are connected to the same Cloud Enterprise Network \(CEN\) instance. This connects the cloud and edge. This topic describes how an enhanced edge node pool works and how to create an enhanced edge node pool.

-   A CEN instance and a CCN instance are created. For more information, see [Create a CEN instance]() and [Create a CCN instance](/intl.en-US/Configuration Guide/Cloud Connect Network/Create a CCN instance.md).
-   Networks are planned to ensure that the CIDR block assigned to edge nodes does not conflict with the CIDR blocks of VPCs.

Edge node pools support two types of modes for collaborative cloud-edge networking: **basic** and **enhanced**.

-   **Basic**: The cloud and edge are connected through Internet connections. Applications in edge node pools cannot access VPCs in the cloud.
-   **Enhanced**: This mode is based on the SDN solution of ACK@Edge. The cloud and edge are connected through high-speed and secure connections. Applications in edge node pools can access VPCs in the cloud. This mode outperforms the **basic** mode in terms of network quality and security.

|Description|Basic|Enhanced|
|-----------|-----|--------|
|Cloud-edge networking|Establish Internet connections.|Create CCN instances.|
|Whether edge nodes can access VPCs|No.|Yes.|
|Network quality|Low.|High. Edge nodes can connect to CCN instances through the nearest access points.|
|Security|Low.|High. Connections between the cloud and edge are encrypted.|
|Costs|Low.|High. Enhanced edge node pools are free of charge during the public preview.|
|Scenarios|Workloads that are deployed at the edge and are not strongly reliant on cloud computing.|Applies to the following scenarios:-   Workloads that require intercommunication between the cloud and edge.
-   Latency-sensitive workloads that require high network quality.
-   Workloads that require high network security. |

## Limits

-   The enhanced mode enables mutual communication between pods at the edge and pods in the cloud, and between pods at the edge and VPCs in the cloud. In addition, edge nodes can access VPCs and pods in the cloud. However, VPCs and pods in the cloud cannot access edge nodes. To enable a VPC to access edge nodes, you must use an elastic IP address \(EIP\).
-   Each enhanced edge node pool must contain at least two AMD64 nodes.
-   In an enhanced edge node pool, the gateway components are installed in pods on edge nodes. These pods support only the Flannel network plug-in. Host networking is not supported.
-   When you create a basic or enhanced edge node pool, you must specify the maximum number of nodes that the edge node pool supports. This value is saved in an annotation of the NodePool object and cannot be modified. We recommend that you set the size of your edge node pool to a proper value.
-   The metadata of an enhanced edge node pool is saved in the annotations of the NodePool object. You must not modify or delete these annotations. Otherwise, the enhanced edge node pool may fail to work in enhanced mode. For more information about the annotations, see [Annotations for an edge node pool](#table_r0v_lda_822).
-   The `openyurt.io/desired-nodepool` label specifies the node pool to which a node belongs. You cannot move a node from an enhanced edge node pool to another enhanced edge node pool by modifying this label. To move the node, you must remove the node from the current node pool and then add it to another enhanced edge node pool. Otherwise, the node cannot work in enhanced mode. For more information, see [Remove edge nodes](/intl.en-US/User Guide for Edge Container Service/Node management/Remove edge nodes.md).

## How an enhanced edge node pool works

Enhanced cloud-edge networking is based on the SDN solution of ACK@Edge and the global network infrastructure of Alibaba Cloud. It enables reliable and secure communication between the cloud and edge. After you create an enhanced edge node pool and add edge nodes to the node pool, the gateway components are automatically installed in pods on edge nodes. The gateway components enable edge nodes to connect to CCN instances through the nearest access points. The CCN instances can communicate with the VPCs that are connected to the same CEN instance. This connects the cloud and edge. In enhanced mode, data exchanged between the cloud and edge is encrypted. Data is transmitted over the internal network of Alibaba Cloud. This ensures the efficiency and security of data transmission. In addition, edge nodes can access services that are deployed in VPCs.

When you create an enhanced edge node pool, the following components are deployed for the node pool: **edge-gateway-core\(egw-core\)** and **edge-gateway-helper\(egw-helper\)**. **edge-gateway-core** is the key component of an enhanced gateway. This component is deployed in the node pool as a Deployment. The Deployment creates and manages two pods in the node pool: one serves as the primary pod while the other serves as the secondary pod. The two pods are deployed on different nodes to ensure high availability. **edge-gateway-helper** is a component that synchronizes routes among nodes. This component is deployed on each node as a DaemonSet. It is used to configure the routing information for nodes.

![G-5](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0817896161/p224127.png)

## Create an enhanced edge node pool

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.

5.  On the Node Pools page, click **Create Edge Node Pool \(Beta\)** in the upper-right corner of the page.

6.  On the **Create Edge Node Pool \(Beta\)** page, set the required parameters. For more information, see [Create an edge node pool](/intl.en-US/User Guide for Edge Container Service/Cell-based management at the edge/Manage edge node pools/Create an edge node pool.md).

    -   Set **Coordination Network between Cloud and Edge** to **Enhanced**.
    -   Select an existing CEN instance from the **CEN Instance** drop-down list and select an existing CCN instance from the **CCN Instance** drop-down list.
7.  Click **Submit**.

8.  After the edge node pool is created, add at least two nodes to the node pool. For more information, see [Add nodes to an edge node pool](/intl.en-US/User Guide for Edge Container Service/Cell-based management at the edge/Manage edge node pools/Add nodes to an edge node pool.md).


## Terms

-   CEN allows you to establish private connections between VPCs in different regions and between VPCs and data centers. This way, network resources are interconnected on a global scale.
-   CCN is a matrix of distributed access gateways. You can connect on-premises resources to Alibaba Cloud by connecting CCN instances to CEN instances.

## Annotations for an edge node pool

|Annotation|Description|
|----------|-----------|
|nodepool.openyurt.io/max-nodes|Specifies the maximum number of nodes that a node pool supports. This annotation is applicable to only non-default edge node pools.|
|nodepool.openyurt.io/pod-cidrs|Specifies the pod CIDR blocks that are assigned to a node pool. This annotation is applicable to only non-default edge node pools.|
|nodepool.openyurt.io/cen-id|The ID of the CEN instance that is specified for an enhanced edge node pool.|
|nodepool.openyurt.io/ccn-id|The ID of the CCN instance that is specified for an enhanced edge node pool.|
|nodepool.openyurt.io/ccn-region|The region of the CCN instance that is specified for an enhanced edge node pool. Only the China \(Shanghai\) region is supported in China.|
|nodepool.openyurt.io/is-default|Specifies whether a node pool is a default edge node pool.|

