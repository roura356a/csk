---
keyword: [Fluid overview, data acceleration]
---

# Overview of Fluid

Fluid is an open source Kubernetes-native distributed dataset orchestrator and accelerator for data-intensive applications in cloud-native scenarios, such as big data applications and AI applications. This topic introduces Fluid and its core features.

## Features

Fluid provides features based on datasets, as shown in the following figure.

![ai-1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8462579161/p233036.png)

-   Native support for dataset abstraction. This feature provides fundamental support for data-intensive applications. It enables efficient data access and improves the cost-effectiveness of data management in multiple aspects.
-   Data warm-up and acceleration in the cloud. Fluid uses the distributed caching services of Alluxio and JindoFS to provide data warm-up and acceleration in the cloud. This also ensures the observability, portability, and automatic horizontal scalability of cached data.
-   Coordinative orchestration of applications and data. When you schedule applications and data in the cloud, you can use this feature to coordinate the orchestration of applications and data based on their characteristics and locations. This improves the efficiency of orchestration.
-   Management of multiple namespaces. This feature allows you to create and manage datasets in multiple namespaces.
-   Management of heterogeneous data sources. This feature allows you to access bottom-layer data from multiple storage types at a time and in a unified manner, such as Object Storage Service \(OSS\), Hadoop Distributed File System \(HDFS\), and Ceph. This applies in hybrid cloud scenarios.

## Concepts

-   Dataset: a set of logically related data that is used by computing engines. For example, Apache Spark uses datasets in big data scenarios and TensorFlow uses datasets in AI scenarios. Datasets enable intelligent applications and help produce the core values in various industries. Dataset management involves multiple aspects, including security, versions, and data acceleration.
-   Runtime: the execution engine that implements security, version management, and data acceleration for datasets. Runtime also defines a series of lifecycle interfaces. These interfaces are used to manage and accelerate datasets.
-   JindoRuntime: the execution engine of JindoFS developed by the Alibaba Cloud Elastic MapReduce \(EMR\) team. JindoRuntime is based on C++ and provides dataset management and caching. JindoRuntime also supports Object Storage Service \(OSS\). Alibaba Cloud provides cloud service-level support for JindoFS. Fluid enables the observability, scalability, and portability of datasets by managing and scheduling JindoRuntime. For more information, see [Use JindoFS to accelerate access to OSS]() and [Use JindoFS to accelerate ResNet-50 model training]().
-   AlluxioRuntime: the execution engine of open source [Alluxio](https://www.alluxio.org/). AlluxioRuntime provides dataset management and caching. AlluxioRuntime supports persistent volume claims \(PVCs\), Ceph, and Cloud Parallel File System \(CPFS\) acceleration. Therefore, you can use AlluxioRuntime in hybrid cloud scenarios. Alluxio is an open source project. Alibaba Cloud will optimize the stability and performance of data caching based on the releases of Alluxio. However, the optimization may have some delays. Fluid enables the observability, scalability, and portability of datasets by managing and scheduling AlluxioRuntime. For more information, see [Fluid Documentation](https://github.com/fluid-cloudnative/fluid/blob/master/docs/zh/TOC.md).

|Feature|Alluxio|Jindo|
|-------|-------|-----|
|Bottom-layer storage|PVC, Ceph, HDFS, CPFS, Network File System \(NFS\), and OSS|OSS|
|Supported by|Open source projects|Alibaba Cloud services|
|Language|Java|C++|
|Support for Prometheus|Yes|No|

**Related topics**  


[Use JindoFS to accelerate access to OSS]()

[Use JindoFS to accelerate ResNet-50 model training]()

