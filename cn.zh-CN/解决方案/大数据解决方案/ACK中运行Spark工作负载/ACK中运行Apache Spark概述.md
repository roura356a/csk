---
keyword: [Apache Spark, ACK, Kubernetes, 数据分析]
---

# ACK中运行Apache Spark概述

Apache Spark是一个在数据分析领域广泛使用的开源项目，它常被应用于众所周知的大数据和机器学习工作负载中。从Apache Spark 2.3.0版本开始，您可以在Kubernetes上运行和管理Spark资源。本文介绍在ACK上运行Apache Spark的优势及涉及的产品介绍。

## 在ACK上运行Apache Spark工作负载的优势

Kubernetes是一个开源容器管理系统，可以提供应用发布、运维、扩缩等机制。容器服务Kubernetes版ACK（Alibaba Cloud Container Service for Kubernetes）是全球首批通过Kubernetes一致性认证的服务平台，提供高性能可伸缩的容器应用管理服务，支持企业级Kubernetes容器化应用的生命周期管理。简化集群的搭建和扩容等运维工作，整合阿里云虚拟化、存储、网络和安全能力，打造云端最佳的Kubernetes容器化应用运行环境。您可以在ACK上运行微服务、批量处理、机器学习等各种工作负载。

在ACK上运行Spark应用具有以下优势：

-   通过把Spark应用和依赖项打包成容器，您可享受容器的各种优点，解决Hadoop版本不匹配和兼容性问题，还可以给容器镜像打上标签控制版本。如果需要测试不同版本的Spark或者依赖项的话，选择对应的版本即可。
-   重用Kubernetes生态的各种组件，比如监控、日志。把Spark工作负载部署在已有的Kubernetes基础设施中，能够快速开始工作，大大减少运维成本。
-   支持多租户，可利用Kubernetes的Namespace和ResourceQuota做用户粒度的资源调度，利用Kubernetes的节点选择机制保证Spark工作负载得到专用的资源。另外，由于Driver Pods创建Executor Pods，您可以用Kubernetes Service Account控制权限，利用Role或者Cluster Role定义细粒度访问权限，安全地运行工作负载，避免受其他工作负载影响。
-   把Spark和管理数据生命周期的应用运行在同一个集群中，可以使用单个编排机制构建端到端生命周期的解决方案，并能复制到其他区域部署，甚至是在私有化环境部署。

## 在ACK上运行Apache Spark工作负载涉及的工具

-   **Spark on Kubernetes Operator**

    [Spark on Kubernetes Operator](https://github.com/AliyunContainerService/spark-on-k8s-operator)帮助您在Kubernetes上像其他工作负载一样用通用的方式方便地运行Spark应用。它使用Kubernetes custom resource来配置、运行Spark应用，并展现其状态。您需要Spark 2.3及以上的版本来支持Kubernetes调度。

-   **Alluxio**

    [Alluxio](https://www.alluxio.io/)一个面向基于云的数据分析和人工智能的开源的数据编排技术。 它为数据驱动型应用和存储系统构建了桥梁，将数据从存储层移动到距离数据驱动型应用更近的位置从而能够更容易被访问。 这还使得应用程序能够通过一个公共接口连接到许多存储系统。Alluxio内存至上的层次化架构使得数据的访问速度能比现有方案快几个数量级。

    在大数据生态系统中，Alluxio位于数据驱动框架或应用（例如Apache Spark、Presto、Tensorflow、Apache HBase、Apache Hive、Apache Flink）和各种持久化存储系统（例如Amazon S3、Google Cloud Storage、OpenStack Swift、HDFS、GlusterFS、IBM Cleversafe、EMC ECS、Ceph、NFS 、Minio、Alibaba OSS）之间。 Alluxio统一了存储在这些不同存储系统中的数据，为其上层数据驱动型应用提供统一的客户端API和全局命名空间。

    ![Alluxio](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0804359951/p158128.jpeg)

    Spark on ACK的大数据解决方案将会采用Alluxio通过缓存的方式加速Spark访问持久化存储系统中的数据。

-   **TPC-DS Benchmark**

    [TPC-DS](http://www.tpc.org/tpcds/)由第三方社区创建和维护，是性能压测，协助确定解决方案的工业标准。这个测试集包含对大数据集的统计、报表生成、联机查询、数据挖掘等功能的复杂应用，测试用的数据和值是有倾斜的，与真实数据一致。可以说TPC-DS是与真实场景非常接近的一个测试集，也是难度较大的一个测试集。

    TPC-DS包含104个Query，覆盖了SQL 2003的大部分标准，有99条压测Query，其中的4条Query各有2个变体（14,23,24,39），最后还有一个`s_max` Query进行全量扫描和最大的一些表的聚合。

    TPC-DS基准测试有以下几个主要特点：

    -   遵循SQL 2003的语法标准，SQL案例比较复杂。
    -   分析的数据量大，并且测试案例是在回答真实的商业问题。
    -   测试案例中包含各种业务模型（例如分析报告型，迭代式的联机分析型，数据挖掘型等）。
    -   几乎所有的测试案例都有很高的IO负载和CPU计算需求。
    ACK中运行Apache Spark的大数据解决方案采用TPC-DS来评测Spark在ACK上的性能。


**相关文档**  


[搭建测试环境](/cn.zh-CN/解决方案/大数据解决方案/ACK中运行Spark工作负载/搭建测试环境.md)

[开发测试代码](/cn.zh-CN/解决方案/大数据解决方案/ACK中运行Spark工作负载/开发测试代码.md)

[在ACK上运行Spark Benchmark](/cn.zh-CN/解决方案/大数据解决方案/ACK中运行Spark工作负载/在ACK上运行Spark Benchmark.md)

[分析测试结果](/cn.zh-CN/解决方案/大数据解决方案/ACK中运行Spark工作负载/分析测试结果.md)

[定位排查问题](/cn.zh-CN/解决方案/大数据解决方案/ACK中运行Spark工作负载/定位排查问题.md)

