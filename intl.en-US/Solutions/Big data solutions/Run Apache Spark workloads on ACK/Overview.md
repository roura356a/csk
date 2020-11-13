---
keyword: [Apache Spark, ACK, Kubernetes, Data analysis]
---

# Overview

Apache Spark is an open source program that is widely used to analyze workloads in scenarios such as big data and machine learning. You can use Kubernetes to run and manage resources on Apache Spark 2.3.0 and later. This topic describes the benefits of running Apache Spark on Alibaba Cloud Container Service for Kubernetes \(ACK\).

## Benefits

Kubernetes is an open source container management system that provides features such as the release, O&M, and scaling of applications. Container Service for Kubernetes \(ACK\) is a high-performance and scalable containerized application management service. It allows you to manage enterprise-level containerized applications throughout the lifecycle. ACK is one of the first Kubernetes platforms that obtain the certificate of conformance from Kubernetes. ACK simplifies the deployment and scale-out operations of Kubernetes clusters and integrates Alibaba Cloud capabilities of virtualization, storage, networking, and security. Based on these capabilities, ACK provides an ideal runtime environment for Kubernetes-based containerized applications. You can run a variety of workloads on ACK, including microservices, data processing in batches, and machine learning.

The following list describes the benefits of running Apache Spark workloads on ACK:

-   By packaging a Spark application and its dependencies in a container, you reap the benefits of containers. For example, you do not have to consider whether your application is compatible with the Hadoop version, and you can attach tags to your container images to control the image versions. This helps you test different versions of Apache Spark or dependencies.
-   You can reuse a variety of Kubernetes add-ons, such as the add-ons for monitoring or logging. By deploying Apache Spark workloads based on the existing Kubernetes infrastructure, you can save time and a large amount of O&M costs.
-   ACK allows multiple tenants to run Apache Spark jobs in an ACK cluster. You can schedule resources for each tenant by using Kubernetes namespaces and resource quotas. ACK provides a mechanism to schedule applications to nodes. This ensures that dedicated resources are used to run each Apache Spark job. The Spark driver pod uses a Kubernetes service account to create executor pods. You can use the service account that is granted a Role or ClusterRole to define fine-grained access permissions. This secures your workloads against the impact of other workloads.
-   ACK deploys Spark and data-centric applications that manage the lifecycle of your data in the same cluster. You can build an end-to-end lifecycle solution by using a single orchestrator and replicate the solution in other regions or even in private clouds.

## Tools

-   **Spark on Kubernetes Operator**

    [Spark on Kubernetes Operator](https://github.com/AliyunContainerService/spark-on-k8s-operator) allows you to run Spark workloads in the same way as you run other workloads on Kubernetes. It uses Kubernetes custom resources to specify, run, and display the running status of Spark jobs. Spark 2.3 or later is required to support resource scheduling in Kubernetes.

-   **Alluxio**

    [Alluxio](https://www.alluxio.io/) is a cloud-based open source data orchestrator that applies to data analysis and artificial intelligence applications. It migrates data from the storage layer of the system to a location that is more accessible for data-driven applications. It also allows applications to connect to multiple storage systems through a common interface. Alluxio stores application data in the memory of each node. This increases the data access speed by several orders of magnitude.

    In the big data ecosystem, Alluxio is located between data-driven frameworks or applications and persistent storage systems. Data-driven frameworks or applications include Apache Spark, Presto, Tensorflow, Apache HBase, Apache Hive, and Apache Flink. Persistent storage systems include Amazon S3, Google Cloud Storage, OpenStack Swift, HDFS, GlusterFS, IBM clever, EMC ECS, Ceph, NFS, Minio, and Alibaba OSS. Alluxio unifies the data stored in these storage systems and provides a unified client API and global namespaces for the data-driven applications that are built based on these storage systems.

    ![Alluxio](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7335780061/p158128.jpeg)

    In the Spark on ACK big data solution, data in the persistent storage systems is cached in Alluxio. This accelerates the Spark access to the data.

-   **TPC-DS Benchmark**

    The [TPC Benchmark DS \(TPC-DS\)](http://www.tpc.org/tpcds/) is a performance testing and decision support benchmark developed and maintained by a third-party community. The TPC-DS test set involves various functions, such as data collection, report generation, online queries, and data mining based on large data sets. Data in real-life scenarios is collected in the test set. Therefore, an application must have high performance to pass the tests based on the test set.

    TPC-DS contains 104 queries that cover most SQL statements in the SQL:2003 standard. TPC-DS contains 99 stress testing queries. Each of the following queries has two variants: 14, 23, 24, and 39. The `s_max` query performs a full scan and aggregates large tables.

    TPC-DS has the following features:

    -   Follows the SQL:2003 standard and provides complex SQL query cases.
    -   Requires analysis of a large amount of data and answers to real business questions.
    -   Provides test cases that include various business models, such as analysis report models, iterative OLAP models, and data mining models.
    -   Requires high I/O load and high CPU utilization in almost all test cases.
    Therefore, the Spark on ACK big data solution uses TPC-DS to evaluate the performance of Spark on ACK.


**Related topics**  


[Set up a test environment]()

[Write test code]()

[Run Spark benchmarks on ACK]()

[Analyze test results]()

[Troubleshooting]()

