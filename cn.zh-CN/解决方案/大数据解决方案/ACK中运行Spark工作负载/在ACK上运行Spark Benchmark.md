---
keyword: [ACK, Spark Benchmark作业, Alluxio分布式缓存加速]
---

# 在ACK上运行Spark Benchmark

准备好测试环境和镜像后，您就可以在ACK上运行Spark Benchmark作业。本文介绍如何生成测试数据和利用测试数据进行Benchmark，以及通过Alluxio进行分布式缓存加速的方法。

[开发测试代码](/cn.zh-CN/解决方案/大数据解决方案/ACK中运行Spark工作负载/开发测试代码.md)

## 测试说明

本文测试先生成1 TB数据，然后分别测试Spark直接从OSS读取数据，和通过Alluxio冷、热缓存做加速的三种情况。

## 生成1 TB数据

执行DataGeneration.scala生成1 TB数据，并将数据存在OSS上，然后Spark SQL查询任务会用到这些数据。具体操作步骤如下。

1.  使用以下模板创建tpcds-data-generator.yaml文件。

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

2.  执行以下命令生成数据。

    ```
    kubectl apply -f tpcds-data-generator.yaml
    ```


## 执行Benchmark任务

查询任务分三次：

1.  第一次直接用Spark读取OSS上的1 TB数据，运行Benchmark。
2.  第二次利用Alluxio做分布式缓存，OSS上的数据会先加载到Alluxio中，Spark从Alluxio中读取缓存数据运行Benchmark。
3.  第三次修改第二次任务中的OSS结果存放路径，此时Alluxio中的缓存数据还在，然后重新运行Benchmark。

通过这三次任务对比，可以看到使用Alluxio缓存加速后，有较大的性能提升。具体操作步骤如下。

1.  从OSS中读取数据，运行Benchmark。

    1.  使用以下模板创建并部署tpcds-benchmark.yaml文件。

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

    2.  执行以下命令，运行Benchmark任务。

        ```
        kubectl apply -f tpcds-benchmark.yaml
        ```

2.  用Alluxio冷缓存，运行Benchmark。

    1.  使用以下模板创建并部署tpcds-benchmark-with-alluxio.yaml文件。

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

    2.  执行以下命令，开始通过Alluxio缓存加速的Benchmark任务。

        ```
        kubectl apply -f tpcds-benchmark-with-alluxio.yaml
        ```

3.  用Alluxio热缓存，运行Benchmark。

    在第一次通过Alluxio做缓存加速时，Benchmark会从OSS读取数据，但是缓存在Alluxio中，所以读取速度比较慢。在第一次使用Alluxio缓存加速Benchmark测试完后，您可以再测试几次对比效果。


[分析测试结果](/cn.zh-CN/解决方案/大数据解决方案/ACK中运行Spark工作负载/分析测试结果.md)

