---
keyword: [yurt-app-manager, edge Kubernetes clusters, edge computing]
---

# yurt-app-manager

The yurt-app-manager component provides cell-based management at the edge for edge Kubernetes clusters. This topic describes usage notes and latest changes to the yurt-app-manager component.

## Introduction

-   In edge computing, edge nodes are classified into groups by zone, region, or other logical attribute such as CPU architecture, Internet service provider \(ISP\), or cloud service provider.
-   Same applications or images may be deployed to different node pools.
-   The backend endpoints of Kubernetes-native Services are arbitrarily distributed across nodes. Consequently, when Service requests are distributed to nodes across groups, these requests may fail to reach the nodes or may not be answered promptly.

ACK@Edge provides a solution to solve these issues, as shown in the following figure.

![G-1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1555896161/p214171.png)

-   Node cell: You can create node pools to manage and maintain hosts in different regions.
-   Application cell: You can deploy workloads to different node pools. This way, you can manage the number of pods and the image version of containers by node pool.
-   Traffic cell: You can configure a Service topology to limit access to Service endpoints. For example, you can expose an application on an edge node to only the current node or other nodes in the same edge node pool.

The yurt-app-manager component supports edge node management \(NodePool\) and workload management \(UnitedDeployment\).

## Usage notes

The NodePool controller is used to create node pools. The UnitedDeployment controller is used to centrally manage multiple workloads. For more information, see [Overview of edge node pools](/intl.en-US/User Guide for Edge Container Service/Cell-based management at the edge/Manage edge node pools/Overview of edge node pools.md) and [Use the UnitedDeployment controller to deploy applications](/intl.en-US/User Guide for Edge Container Service/Cell-based management at the edge/Use the UnitedDeployment controller to deploy applications.md).

**Note:** By default, the yurt-app-manager component is installed when an edge Kubernetes cluster is created. For more information about how to upgrade the components of an edge Kubernetes cluster, see [Upgrade components in an edge Kubernetes cluster](/intl.en-US/User Guide for Edge Container Service/Edge cluster management/Upgrade components in an edge Kubernetes cluster.md).

## Release notes

**January 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.9.11-c2c8cce-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/yurt-app-manager:v0.9.11-c2c8cce-aliyun|2021-01-14|The yurt-app-manager component is released.|No impacts on your workloads.|

