---
keyword: [ACK, Spark benchmarks, Query acceleration based on the Alluxio distributed cache]
---

# Run Spark benchmarks on ACK

After the test environment and test image are ready, you can run Spark benchmarks on ACK. This topic describes how to generate test data, run benchmarks based on the test data, and accelerate queries by using the Alluxio distributed cache.

[Write test code](/intl.en-US/Solutions/Big data solutions/Run Apache Spark workloads on ACK/Write test code.md)

## Test description:

In this test, 1 TB of data is generated to test the performance of Spark on ACK in the following scenarios: Spark reads data from OSS. Spark uses the Alluxio cold cache to accelerate data access. Spark uses the Alluxio warm cache to accelerate data access.

## Generate 1 TB of test data

Run the DataGeneration.scala command to generate 1 TB of data, and store the data in OSS for Spark SQL queries. Perform the following steps:

1.  Use the following template to create the tpcds-data-generator.yaml file:

    ```
    apiVersion: "sparkoperator.k8s.io/v1beta2"
    kind: SparkApplication
    metadata:
      name: tpcds-data-generation
      namespace: default
    spec:
      type: Scala
      image: registry.cn-beijing.aliyuncs.com/yukong/ack-spark-benchmark:1.0.1
      sparkVersion: 2.4.5
      mainClass: com.aliyun.spark.benchmark.tpcds.DataGeneration
      mainApplicationFile: "local:///opt/spark/jars/ack-spark-benchmark-assembly-0.1.jar"
      mode: cluster
      arguments:
        # TPC-DS data localtion
        - "oss://cloudnativeai/spark/data/tpc-ds-data/1000g"
        # Path to kit in the docker image
        - "/tmp/tpcds-kit/tools"
        # Data Format
        - "parquet"
        # Scale factor (in GB)
        - "1000"
        # Generate data num partitions
        - "100"
        # Create the partitioned fact tables
        - "false"
        # Shuffle to get partitions coalesced into single files.
        - "false"
        # Logging set to WARN
        - "true"
      hadoopConf:
        # OSS
        "fs.oss.impl": "org.apache.hadoop.fs.aliyun.oss.AliyunOSSFileSystem"
        "fs.oss.endpoint": "oss-cn-beijing-internal.aliyuncs.com"
        "fs.oss.accessKeyId": "YOUR-ACCESS-KEY-ID"
        "fs.oss.accessKeySecret": "YOUR-ACCESS-KEY-SECRET"
      sparkConf:
        "spark.kubernetes.allocation.batch.size": "100"
        "spark.sql.adaptive.enabled": "true"
        "spark.eventLog.enabled": "true"
        "spark.eventLog.dir": "oss://cloudnativeai/spark/spark-events"
      driver:
        cores: 6
        memory: "20480m"
        serviceAccount: spark
      executor:
        instances: 20
        cores: 8
        memory: "61440m"
        memoryOverhead: 2g
      restartPolicy:
        type: Never
    ```

2.  Run the following command to generate data:

    ```
    kubectl apply -f tpcds-data-generator.yaml
    ```


## Run the benchmarks

Query data for three times:

1.  Spark reads 1 TB of data from OSS.
2.  Alluxio caches data from OSS in a distributed manner. Spark reads data from the Alluxio cold cache.
3.  After you modify the OSS path where the data is stored in the preceding scenario, Spark reads data from the Alluxio warm cache.

The test results show that the Alluxio cache improves the query performance. The following content describes the procedure:

1.  Run the benchmark in the scenario where Spark reads data from OSS.

    1.  Use the following template to create and deploy the tpcds-benchmark.yaml file:

        ```
        apiVersion: "sparkoperator.k8s.io/v1beta2"
        kind: SparkApplication
        metadata:
          name: tpcds-benchmark
          namespace: default
        spec:
          type: Scala
          mode: cluster
          image: registry.cn-beijing.aliyuncs.com/yukong/ack-spark-benchmark:1.0.1
          imagePullPolicy: Always
          sparkVersion: 2.4.5
          mainClass: com.aliyun.spark.benchmark.tpcds.BenchmarkSQL
          mainApplicationFile: "local:///opt/spark/jars/ack-spark-benchmark-assembly-0.1.jar"
          arguments:
            # TPC-DS data localtion
            - "oss://cloudnativeai/spark/data/tpc-ds-data/1000g"
            # results location
            - "oss://cloudnativeai/spark/result/tpcds-benchmark-result-1000g"
            # Path to kit in the docker image
            - "/tmp/tpcds-kit/tools"
            # Data Format
            - "parquet"
            # Scale factor (in GB)
            - "1000"
            # Number of iterations
            - "1"
            # Optimize queries
            - "false"
            # Filter queries, will run all if empty - "q70-v2.4,q82-v2.4,q64-v2.4"
            - ""
            # Logging set to WARN
            - "true"
          hostNetwork: true
          dnsPolicy: ClusterFirstWithHostNet
          restartPolicy:
            type: Never
          timeToLiveSeconds: 86400
          hadoopConf:
            # OSS
            "fs.oss.impl": "org.apache.hadoop.fs.aliyun.oss.AliyunOSSFileSystem"
            "fs.oss.endpoint": "oss-cn-beijing-internal.aliyuncs.com"
            "fs.oss.accessKeyId": "YOUR-ACCESS-KEY-ID"
            "fs.oss.accessKeySecret": "YOUR-ACCESS-KEY-SECRET"
          sparkConf:
            "spark.kubernetes.allocation.batch.size": "200"
            "spark.sql.adaptive.join.enabled": "true"
            "spark.eventLog.enabled": "true"
            "spark.eventLog.dir": "oss://cloudnativeai/spark/spark-events"
          driver:
            cores: 5
            memory: "20480m"
            labels:
              version: 2.4.5
              spark-app: spark-tpcds
              role: driver
            serviceAccount: spark
          executor:
            cores: 7
            instances: 20
            memory: "20480m"
            memoryOverhead: "8g"
            labels:
              version: 2.4.5
              role: executor
        ```

    2.  Run the following command to run the benchmark:

        ```
        kubectl apply -f tpcds-benchmark.yaml
        ```

2.  Run the benchmark in the scenario where Spark reads data from the Alluxio cold cache.

    1.  Use the following template to create and deploy the tpcds-benchmark-with-alluxio.yaml file:

        ```
        apiVersion: "sparkoperator.k8s.io/v1beta2"
        kind: SparkApplication
        metadata:
          name: tpcds-benchmark-sql
          namespace: default
        spec:
          type: Scala
          mode: cluster
          image: registry.cn-beijing.aliyuncs.com/yukong/ack-spark-benchmark:1.0.1
          imagePullPolicy: Always
          sparkVersion: 2.4.5
          mainClass: com.aliyun.spark.benchmark.tpcds.BenchmarkSQL
          mainApplicationFile: "local:///opt/spark/jars/ack-spark-benchmark-assembly-0.1.jar"
          arguments:
            # TPC-DS data localtion
            - "alluxio://alluxio-master-0.alluxio.svc.cluster.local:19998/spark/data/tpc-ds-data/1000g"
            # results location
            - "oss://cloudnativeai/spark/result/tpcds-benchmark-result-1000g-alluxio"
            # Path to kit in the docker image
            - "/tmp/tpcds-kit/tools"
            # Data Format
            - "parquet"
            # Scale factor (in GB)
            - "1000"
            # Number of iterations
            - "1"
            # Optimize queries
            - "false"
            # Filter queries, will run all if empty - "q70-v2.4,q82-v2.4,q64-v2.4"
            - ""
            # Logging set to WARN
            - "true"
          hostNetwork: true
          dnsPolicy: ClusterFirstWithHostNet
          restartPolicy:
            type: Never
          timeToLiveSeconds: 86400
          hadoopConf:
            # OSS
            "fs.oss.impl": "org.apache.hadoop.fs.aliyun.oss.AliyunOSSFileSystem"
            "fs.oss.endpoint": "oss-cn-beijing-internal.aliyuncs.com"
            "fs.oss.accessKeyId": "YOUR-ACCESS-KEY-ID"
            "fs.oss.accessKeySecret": "YOUR-ACCESS-KEY-SECRET"
          sparkConf:
            "spark.kubernetes.allocation.batch.size": "200"
            "spark.sql.adaptive.join.enabled": "true"
            "spark.eventLog.enabled": "true"
            "spark.eventLog.dir": "oss://cloudnativeai/spark/spark-events"
          volumes:
            - name: "spark-local-dir-1"
              hostPath:
                path: "/mnt/disk1"
                type: Directory
            - name: "spark-local-dir-2"
              hostPath:
                path: "/mnt/disk2"
                type: Directory
            - name: "spark-local-dir-3"
              hostPath:
                path: "/mnt/disk3"
                type: Directory
            - name: "spark-local-dir-4"
              hostPath:
                path: "/mnt/disk4"
                type: Directory
            - name: "spark-local-dir-5"
              hostPath:
                path: "/mnt/disk5"
                type: Directory
            - name: "spark-local-dir-6"
              hostPath:
                path: "/mnt/disk6"
                type: Directory
            - name: "spark-local-dir-7"
              hostPath:
                path: "/mnt/disk7"
                type: Directory
            - name: "spark-local-dir-8"
              hostPath:
                path: "/mnt/disk8"
                type: Directory
            - name: "spark-local-dir-9"
              hostPath:
                path: "/mnt/disk9"
                type: Directory
            - name: "spark-local-dir-10"
              hostPath:
                path: "/mnt/disk10"
                type: Directory
            - name: "spark-local-dir-11"
              hostPath:
                path: "/mnt/disk11"
                type: Directory
            - name: "spark-local-dir-12"
              hostPath:
                path: "/mnt/disk12"
                type: Directory
          driver:
            cores: 5
            memory: "20480m"
            labels:
              version: 2.4.5
              spark-app: spark-tpcds
              role: driver
            serviceAccount: spark
          executor:
            cores: 7
            instances: 20
            memory: "20480m"
            memoryOverhead: "8g"
            labels:
              version: 2.4.5
              role: executor
            volumeMounts:
              - name: "spark-local-dir-1"
                mountPath: "/mnt/disk1"
              - name: "spark-local-dir-2"
                mountPath: "/mnt/disk2"
              - name: "spark-local-dir-3"
                mountPath: "/mnt/disk3"
              - name: "spark-local-dir-4"
                mountPath: "/mnt/disk4"
              - name: "spark-local-dir-5"
                mountPath: "/mnt/disk5"
              - name: "spark-local-dir-6"
                mountPath: "/mnt/disk6"
              - name: "spark-local-dir-7"
                mountPath: "/mnt/disk7"
              - name: "spark-local-dir-8"
                mountPath: "/mnt/disk8"
              - name: "spark-local-dir-9"
                mountPath: "/mnt/disk9"
              - name: "spark-local-dir-10"
                mountPath: "/mnt/disk10"
              - name: "spark-local-dir-11"
                mountPath: "/mnt/disk11"
              - name: "spark-local-dir-12"
                mountPath: "/mnt/disk12"
        ```

    2.  Run the following command to run the benchmark:

        ```
        kubectl apply -f tpcds-benchmark-with-alluxio.yaml
        ```

3.  Run the benchmark in the scenario where Spark reads data from the Alluxio warm cache.

    When you use Alluxio to accelerate data access for the first time, Spark reads data from OSS and caches the data in Alluxio. In this case, the read speed is slow. Therefore, we recommend that you run more benchmarks after the preceding step. In these tests, Spark reads data from the Alluxio hot cache.


[Analyze test results](/intl.en-US/Solutions/Big data solutions/Run Apache Spark workloads on ACK/Analyze test results.md)

