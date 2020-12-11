---
keyword: [managed edge Kubernetes cluster, edge computing, edge, edge network, network autonomy, tunneling for edge node management]
---

# ACK@Edge overview

Container Service for Kubernetes@Edge \(ACK@Edge\) is a cloud-managed solution that is provided by Alibaba Cloud to coordinate cloud and edge computing. This topic describes the background and features of managed edge Kubernetes clusters. This topic also provides further details about network autonomy and tunneling for edge node management.

## Background

With the emergence of the fifth generation technology standard for cellular networks \(5G\) and Internet of Things \(IoT\), the number of smart devices connected to the Internet are rapidly increasing. Traditional cloud computing platforms that provide computing and storage services on the cloud cannot meet the requirements from edge devices for time-efficient computing, larger storage capacity, and enhanced computing capacity. The solution is mainly designed to bring cloud computing to edges \(clients\) and perform the delivery, management, and maintenance of cloud computing in the ACK console.

A managed edge Kubernetes cluster is a standard, secure, and highly-available Kubernetes cluster deployed in the cloud. This type of cluster is integrated with features of Alibaba Cloud, such as virtualization, storage, networking, and security. This simplifies the management and maintenance of clusters and allows you to focus on your business development. ACK@Edge provides the following features:

-   Manages edge nodes in the cloud and allows you to build a cloud-native infrastructure for edge computing.
-   Allows you to quickly connect edge computing resources to the cloud. These resources include IoT gateway devices, terminals, Content Delivery Network \(CDN\) resources, and data centers.
-   Supports the x86 and Advanced RISC Machine \(ARM\) architectures.
-   Applies to diverse scenarios, such as edge intelligence, intelligent buildings, intelligent factories, audio and video live streaming, online education, and CDN.

Managed edge Kubernetes clusters support features such as node autonomy, processing units, network traffic management, and native APIs for the management and maintenance of resources at the edge side. To use these features, you do not need to rewrite the logic of your services. This provides a native and centralized method for application lifecycle management and resource scheduling in edge computing scenarios.

## Features

![Managed edge Kubernetes cluster architecture](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9783068951/p99748.png)

Managed edge Kubernetes clusters provide the following features to support lifecycle management for containerized applications and resources in edge computing scenarios:

-   Allows you to quickly create highly available edge Kubernetes clusters and provides lifecycle management on edge Kubernetes clusters, such as scaling, upgrading, logging, and monitoring. You can perform the preceding operations in the ACK console.
-   Supports access to various resources for edge computing, such as data centers,IoT devices, and the x86 and ARM architectures. Hybrid scheduling of resources for heterogeneous computing is also supported.
-   Supports node autonomy and network autonomy to ensure the reliability of edge nodes and services in edge computing scenarios where the network connection is weak.
-   Supports reverse tunneling for management and maintenance of edge nodes.
-   Allows you to deploy and manage processing units, and manage network traffic that is received by these processing units.

## Network autonomy at the edge

By default, network autonomy is enabled for a node in a managed edge Kubernetes cluster. If an error occurs, the network is automatically recovered.

-   On an edge node, each pod IP address is bound to a pod name. No matter whether the application or the node is restarted, the pod IP addresses remain unchanged. The MAC address of the VXLAN tunnel endpoint \(VTEP\) that is provided by the flannel.1 virtual network interface for a node is bound to the name of the node. No matter whether the container where flannel is deployed is restarted or the node where the container is deployed is restarted, the MAC address of the VTEP remains unchanged.
-   If an error occurs to the network between an edge node and the cloud, you can restart the edge node or the applications deployed on the edge node to automatically restore all network connections related to the applications. This feature applies to cross-node communication in edge computing scenarios where the network connection is weak.

No matter whether an application is deployed in the host network or not, network autonomy is enabled for the application pods. This ensures that the network communications between applications can be automatically restored after applications are restarted from exceptions. The following figure shows the architecture.

![Network autonomy at the edge](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9783068951/p100694.png)

**Note:** If a pod is deleted or migrated to another node, the IP address of the pod is changed.

## Tunneling for management of edge nodes

In a native Kubernetes cluster, cloud management components such as kube-apiserver must access kubelet on each edge node. The components must directly run commands, such as `kubectl logs/exec`, on edge nodes to manage the edge nodes. Monitoring components such as metrics-server must collect metrics of edge nodes from the cloud. However, a managed edge Kubernetes cluster cannot be directly accessed from the cloud if the edge nodes in the cluster are deployed in a virtual private cloud \(VPC\).

To improve user experience, ACK provides a solution for you to access edge nodes that are deployed in a VPC from the cloud. After you create a managed edge Kubernetes cluster, the edge-tunnel-server and edge-tunnel-agent components are automatically deployed in the created cluster by ACK@Edge to create tunnels between the cloud and edge.

-   The edge-tunnel-server component is deployed as a Deployment on a node in the cloud.

    Therefore, when you create a managed edge Kubernetes cluster, you must purchase at least one Elastic Compute Service \(ECS\) instance to deploy the component.

-   The edge-tunnel-agent component is deployed as a DaemonSet on each edge node.

To create secure and encrypted tunnels over the Internet, ACK@Edge purchases a Server Load Balancer \(SLB\) instance. The edge-tunnel-agent component on each edge node establishes a secure and encrypted tunnel to the cloud through this SLB instance. The following figure shows the architecture.

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9783068951/p101538.png)

**Note:**

-   When edge nodes are disconnected from the cloud or the network connection is weak, the tunnels may fail to work as expected.
-   If you delete or stop the SLB instance that is used by the tunnels, the tunnels cannot work as expected.

**Related topics**  


[Create a managed edge cluster](/intl.en-US/User Guide for Edge Container Service/Edge cluster management/Create a managed edge cluster.md)

[Upgrade an edge cluster](/intl.en-US/User Guide for Edge Container Service/Edge cluster management/Upgrade an edge cluster.md)

[Expand an edge cluster](/intl.en-US/User Guide for Edge Container Service/Edge cluster management/Expand an edge cluster.md)

