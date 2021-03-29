---
keyword: [cell-based management at the edge, edge computing, ACK@Edge]
---

# Overview of cell-based management at the edge

In edge computing, nodes that belong to different groups are isolated from each other in one way or another. For example, these nodes may be disconnected, may not share the same resources, may have heterogeneous resources, or may run applications that are independently deployed. ACK@Edge provides a cell-based management solution for edge computing scenarios. This topic describes how to implement cell-based management at the edge.

## Traditional management

-   In edge computing, edge nodes are classified into groups by zone, region, or other logical attribute such as CPU architecture, Internet service provider \(ISP\), or cloud service provider.
-   Same applications or images may be deployed to different node pools.
-   The backend endpoints of Kubernetes-native Services are arbitrarily distributed across nodes. Consequently, when Service requests are distributed to nodes across groups, these requests may fail to reach the nodes or may not be answered promptly.

## Cell-based management at the edge

ACK@Edge provides a solution to solve these issues, as shown in the following figure.

![G-1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1555896161/p214171.png)

-   Node cell: You can create node pools to manage and maintain hosts in different regions.
-   Application cell: You can deploy workloads to different node pools. This way, you can manage the number of pods and the image version of containers by node pool.
-   Traffic cell: You can configure a Service topology to limit access to Service endpoints. For example, you can expose an application on an edge node to only the current node or other nodes in the same edge node pool.

