# Use ASK to create Spark tasks

In a serverless Kubernetes \(ASK\) cluster, you can create pods as needed. The system stops billing after a pod is stopped and you do not need to reserve computing resources to handle Spark tasks. This resolves the issues of insufficient computing resources and saves you the need to expand the cluster. In addition, you can reduce the computing costs by using preemptible instances. This topic describes how to use ASK to create Spark tasks to meet your business requirements.

-   [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md)
-   [Use kubectl to connect to an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Cluster management/Manage and access clusters/Use kubectl to connect to an ASK cluster.md)

## Procedure

1.  Deploy the ack-spark-operator chart by using one of the following methods:

    -   Log on to the [Container Service for Kubernetes console](https://cs.console.aliyun.com). In the left-side navigation pane, choose **Marketplace** \> **App Catalog** and select ack-spark-operator to deploy the chart.
    -   Run the helm command to manually deploy the chart.

        **Note:** The helm version must be V3 or later.

        ```
        #Create a service account
        kubectl create serviceaccount spark
        #Bind permissions
        kubectl create clusterrolebinding spark-role --clusterrole=edit --serviceaccount=default:spark --namespace=default
        #Install the operator
        helm repo add incubator http://storage.googleapis.com/kubernetes-charts-incubator
        helm install incubator/sparkoperator --namespace default  --set operatorImageName=registry.cn-hangzhou.aliyuncs.com/acs/spark-operator  --set operatorVersion=ack-2.4.5-latest  --generate-name
        ```

    After you deploy the chart, run the following command to check whether spark-operator is started:

    ```
    kubectl -n spark-operator get pod
    ```

    ```
    NAME                                  READY   STATUS      RESTARTS   AGE
    ack-spark-operator-7698586d7b-pvwln   1/1     Running     0          5m9s
    ack-spark-operator-init-26tvh         0/1     Completed   0          5m9s
    ```

2.  Create the spark-pi.yaml file and copy the following content into the file:

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

3.  Run the following command to deploy a Spark task:

    ```
    kubectl apply -f spark-pi.yaml
    ```

4.  Run the following commands to check the state of the pod.

    Run the following command to deploy a Spark task:

    ```
    kubectl apply -f spark-pi.yaml
    ```

    ```
    sparkapplication.sparkoperator.k8s.io/spark-pi created
    ```

    Run the following command to query the pod:

    ```
    kubectl get pod
    ```

    ```
    NAME              READY   STATUS    RESTARTS   AGE
    spark-pi-driver   1/1     Running   0          2m12s
    ```

    Run the following command to query the pod:

    ```
    kubectl get pod
    ```

    ```
    NAME              READY   STATUS      RESTARTS   AGE
    spark-pi-driver   0/1     Completed   0          2m54s
    ```

5.  Run the following command to view the computing result of the Spark task:

    ```
    kubectl logs spark-pi-driver|grep Pi
    ```

    ```
    20/04/30 07:27:51 INFO DAGScheduler: ResultStage 0 (reduce at SparkPi.scala:38) finished in 11.031 s
    20/04/30 07:27:51 INFO DAGScheduler: Job 0 finished: reduce at SparkPi.scala:38, took 11.137920 s
    Pi is roughly 3.1414371514143715
    ```

6.  To use a preemptible instance, add a preemptible instance annotation to the pod.

    For more information about how to add a preemptible instance annotation, see [Use preemptible instances](/intl.en-US/User Guide for Serverless Kubernetes Clusters/ECI Pod management/Use preemptible instances.md).


