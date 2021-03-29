---
keyword: [edge node pools, ACK@Edge, edge computing]
---

# Overview of edge node pools

The **yurt-app-manager** component provided by ACK@Edge allows you to manage edge node pools in edge computing scenarios. Edge nodes are distributed to different edge node pools based on specified attributes. This way, you can centrally manage and maintain edge nodes that are deployed in different regions by edge node pool. This topic describes edge node pools and how edge nodes are managed by edge node pool.

## Traditional node management

In edge computing scenarios, edge nodes can be classified by different attributes such as CPU architecture, Internet service provider \(ISP\), and cloud service provider. Traditionally, labels are used to classify and manage nodes. However, as the numbers of nodes and labels increase, it becomes more complex to manage and maintain nodes. The following figure shows the traditional way of node management.

![G-2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8746896161/p214201.png)

## Edge node pools

Edge node pools allow you to classify nodes from a different dimension. You can centrally manage and maintain edge nodes that are deployed in different regions by edge node pool, as shown in the following figure.

![G-3](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8746896161/p214208.png)

For more information, see [Overview of cell-based management at the edge](/intl.en-US/User Guide for Edge Container Service/Cell-based management at the edge/Overview of cell-based management at the edge.md).

**Related topics**  


[Create an edge node pool]()

[Add nodes to an edge node pool]()

[Create an enhanced edge node pool \(public preview\)]()

