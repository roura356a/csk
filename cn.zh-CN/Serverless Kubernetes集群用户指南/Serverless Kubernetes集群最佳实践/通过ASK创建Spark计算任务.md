---
keyword: Spark计算任务
---

# 通过ASK创建Spark计算任务

在ASK集群中，您可以按需按量创建Pod。当Pod结束后停止收费，无需为Spark计算任务预留计算资源，从而摆脱集群计算力不足和扩容的烦扰，同时结合抢占式实例可以降低任务的计算成本。本文主要为您介绍如何通过ASK按需创建Spark计算任务。

-   [创建Serverless Kubernetes集群](/cn.zh-CN/Serverless Kubernetes集群用户指南/快速入门/创建Serverless Kubernetes集群.md)
-   [通过kubectl连接Kubernetes集群](/cn.zh-CN/Serverless Kubernetes集群用户指南/集群/管理和访问集群/通过kubectl连接Kubernetes集群.md)

## 操作步骤

1.  部署ack-spark-operator Chart，可以通过以下两种方式：

    -   在[容器服务管理控制台](https://cs.console.aliyun.com)的导航栏中选择**市场** \> **应用目录**，通过选择ack-spark-operator来进行部署。
    -   通过helm命令行手动安装。

        **说明：** 要求Helm的版本不低于V3。

        ```
        #创建serviceaccount
        kubectl create serviceaccount spark
        #绑定权限
        kubectl create clusterrolebinding spark-role --clusterrole=edit --serviceaccount=default:spark --namespace=default
        #安装operator
        helm repo add incubator http://storage.googleapis.com/kubernetes-charts-incubator
        helm install incubator/sparkoperator --namespace default  --set operatorImageName=registry.cn-hangzhou.aliyuncs.com/acs/spark-operator  --set operatorVersion=ack-2.4.5-latest  --generate-name
        ```

    部署后可以执行以下命令确认spark-operator已经启动成功。

    ```
    kubectl -n spark-operator get pod
    ```

    预期输出：

    ```
    NAME                                  READY   STATUS      RESTARTS   AGE
    ack-spark-operator-7698586d7b-pvwln   1/1     Running     0          5m9s
    ack-spark-operator-init-26tvh         0/1     Completed   0          5m9s
    ```

2.  创建spark-pi.yaml文件并拷贝以下内容到该文件。

    ```
    apiVersion: "sparkoperator.k8s.io/v1beta2"
    kind: SparkApplication
    metadata:
      name: spark-pi
      namespace: default
    spec:
      arguments:
      - "1000"
      sparkConf:
        "spark.scheduler.maxRegisteredResourcesWaitingTime": "3000s"
        "spark.kubernetes.allocation.batch.size": "1"
        "spark.rpc.askTimeout": "36000s"
        "spark.network.timeout": "36000s"
        "spark.rpc.lookupTimeout": "36000s"
        "spark.core.connection.ack.wait.timeout": "36000s"
        "spark.executor.heartbeatInterval": "10000s"
      type: Scala
      mode: cluster
      image: "registry.cn-shenzhen.aliyuncs.com/ringtail/spark-pi:0.4"
      imagePullPolicy: Always
      mainClass: org.apache.spark.examples.SparkPi
      mainApplicationFile: "local:///opt/spark/examples/jars/spark-examples_2.11-2.4.5.jar"
      sparkVersion: "2.4.5"
      restartPolicy:
        type: Never
      args:
      driver:
        cores: 4
        coreLimit: "4"
        annotations:
          k8s.aliyun.com/eci-image-cache: "true"
        memory: "6g"
        memoryOverhead: "2g"
        labels:
          version: 2.4.5
        serviceAccount: spark
      executor:
        annotations:
          k8s.aliyun.com/eci-image-cache: "true"
        cores: 2
        instances: 1
        memory: "3g"
        memoryOverhead: "1g"
        labels:
          version: 2.4.5
    ```

3.  执行以下命令，部署一个Spark计算任务。

    ```
    kubectl apply -f spark-pi.yaml
    ```

4.  执行以下命令，部署Spark计算任务。

    ```
    kubectl apply -f spark-pi.yaml
    ```

    预期输出：

    ```
    sparkapplication.sparkoperator.k8s.io/spark-pi created
    ```

    执行以下命令，查看Pod的运行状态。

    ```
    kubectl get pod
    ```

    预期输出：

    ```
    NAME              READY   STATUS    RESTARTS   AGE
    spark-pi-driver   1/1     Running   0          2m12s
    ```

    执行以下命令，查看Pod的运行状态。

    ```
    kubectl get pod
    ```

    预期输出：

    ```
    NAME              READY   STATUS      RESTARTS   AGE
    spark-pi-driver   0/1     Completed   0          2m54s
    ```

5.  执行以下命令，查看Spark任务的计算结果。

    ```
    kubectl logs spark-pi-driver|grep Pi
    ```

    预期输出：

    ```
    20/04/30 07:27:51 INFO DAGScheduler: ResultStage 0 (reduce at SparkPi.scala:38) finished in 11.031 s
    20/04/30 07:27:51 INFO DAGScheduler: Job 0 finished: reduce at SparkPi.scala:38, took 11.137920 s
    Pi is roughly 3.1414371514143715
    ```

6.  通过给Pod加上抢占式实例的Annotation，使用抢占式实例。

    关于抢占式实例Annotation的用法，请参见[使用抢占式实例](/cn.zh-CN/Serverless Kubernetes集群用户指南/ECI Pod/使用抢占式实例.md)。


