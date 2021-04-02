---
keyword: [Alluxio distributed cache acceleration, Spark SQL, ACK, Performance comparison]
---

# Analyze test results

This topic compares the performance of ACK-based Spark SQL queries on 1 TB of data before and after the Alluxio distributed cache is used.

[Run Spark benchmarks on ACK](/intl.en-US/Solutions/Big data solutions/Run Apache Spark workloads on ACK/Run Spark benchmarks on ACK.md)

## Hardware configurations

The following table lists the ACK cluster configurations.

|Cluster type|Standard dedicated cluster|
|------------|--------------------------|
|Elastic Compute Service \(ECS\) instance|-   Instance type: ecs.d1ne.6xlarge
-   Alibaba Cloud Linux 2.1903
-   CPU: 24 cores
-   Memory: 96 GB
-   Disk size: 5,500 GB. Disk type: HDD. |
|Number of worker nodes|20|

## Software configurations

-   Software version
    -   Apache Spark: 2.4.5
    -   Alluxio: 2.3.0
-   Spark configurations

    |Parameter|Value|
    |---------|-----|
    |spark.driver.cores|5|
    |spark.driver.memory \(MB\)|20480|
    |spark.executor.cores|7|
    |spark.executor.memory \(MB\)|20480|
    |spark.executor.instances|20|


## Test results

The following table lists the amount of time consumed by the tests based on each benchmark. The queries are performed on 1 TB of data one after another.

|Benchmark|Total time consumed by 104 queries \(Unit: minutes\)|
|---------|----------------------------------------------------|
|Spark with OSS|180|
|Spark with Alluxio Cold|145|
|Spark with Alluxio Warm|137|

The following figure shows the amount of time consumed by each query.

![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9835780061/p161690.jpeg)

## Conclusions

The test results show that the query performance is improved after the Alluxio cache is used. The first time Alluxio is used, the query performance is not high because Alluxio has to cache data from Object Storage Service \(OSS\). The query performance will be greatly improved in subsequent tests.

[Troubleshooting](/intl.en-US/Solutions/Big data solutions/Run Apache Spark workloads on ACK/Troubleshooting.md)

