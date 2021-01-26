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

**Related topics**  


[Create a managed edge Kubernetes cluster](/intl.en-US/User Guide for Edge Container Service/Edge cluster management/Create a managed edge cluster.md)

[Upgrade an edge cluster](/intl.en-US/User Guide for Edge Container Service/Edge cluster management/Upgrade an edge cluster.md)

[Expand an edge cluster](/intl.en-US/User Guide for Edge Container Service/Edge cluster management/Expand an edge cluster.md)

