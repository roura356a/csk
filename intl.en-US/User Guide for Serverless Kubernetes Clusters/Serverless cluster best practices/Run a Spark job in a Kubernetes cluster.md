Run a Spark job in a Kubernetes cluster 
============================================================



Spark on Kubernetes 
----------------------------------------

**How to use Spark:** 

**Install Kubernetes Operator for Apache Spark** 

Kubernetes Operator for Apache Spark is designed for running Spark jobs in Kubernetes clusters. It allows you to submit Spark tasks that are defined in custom resource definition (CRD) files to Kubernetes clusters. Kubernetes Operator for Apache Spark provides the following benefits:

1. Compared with open source Apache Spark, Kubernetes Operator for Apache Spark provides more features. 

2. Kubernetes Operator for Apache Spark can be integrated with the storage, monitoring, and logging components in a Kubernetes cluster. 

3. Kubernetes Operator for Apache Spark supports advanced Kubernetes features such as disaster recovery and auto scaling. In addition, Kubernetes Operator for Apache Spark can also optimize resource scheduling. 


**Note**

To install Kubernetes Operator for Apache Spark, search for ack-spark-operator on the App Catalog page in the Container Service for Kubernetes (ACK) console. Set sparkJobNamespace to the namespace where you want to deploy the Spark job. The default value is default. An empty string indicates that the Spark job monitors all namespaces.

The Spark job also requires a service account to assign pod permissions. You can set kind to serviceAccount, role, or rolebinding, and modify the namespace to create service accounts and assign pod permissions, as shown in the following code block:




    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: spark
      namespace: default
    ---
    apiVersion: rbac.authorization.k8s.io/v1
    kind: Role
    metadata:
      namespace: default
      name: spark-role
    rules:
    - apiGroups: [""]
      resources: ["pods"]
      verbs: ["*"]
    - apiGroups: [""]
      resources: ["services"]
      verbs: ["*"]
    ---
    apiVersion: rbac.authorization.k8s.io/v1
    kind: RoleBinding
    metadata:
      name: spark-role-binding
      namespace: default
    subjects:
    - kind: ServiceAccount
      name: spark
      namespace: default
    roleRef:
      kind: Role
      name: spark-role
      apiGroup: rbac.authorization.k8s.io





**Create a job image** 

Compile the JAR packages of the Spark job into a Dockerfile. In the following example, the image of Kubernetes Operator for Apache Spark that is provided by Alibaba Cloud is used. Sample Dockerfile:



    FROM registry.cn-hangzhou.aliyuncs.com/acs/spark:ack-2.4.5-latest
    RUN mkdir -p /opt/spark/jars
    # If you want to read data from or sink scheduled events to Object Storage Service (OSS), add the following JAR packages to the image:
    ADD https://repo1.maven.org/maven2/com/aliyun/odps/hadoop-fs-oss/3.3.8-public/hadoop-fs-oss-3.3.8-public.jar $SPARK_HOME/jars
    ADD https://repo1.maven.org/maven2/com/aliyun/oss/aliyun-sdk-oss/3.8.1/aliyun-sdk-oss-3.8.1.jar $SPARK_HOME/jars
    ADD https://repo1.maven.org/maven2/org/aspectj/aspectjweaver/1.9.5/aspectjweaver-1.9.5.jar $SPARK_HOME/jars
    ADD https://repo1.maven.org/maven2/org/jdom/jdom/1.1.3/jdom-1.1.3.jar $SPARK_HOME/jars
    COPY SparkExampleScala-assembly-0.1.jar /opt/spark/jars




**Notice**

Alibaba Cloud provides the Spark 2.4.5 base image, which is optimized for resource scheduling and auto scaling in Kubernetes clusters. We recommend that you use this image.

**Cache an image to accelerate image pulling** 

The size of a Spark image can be large. It may be time-consuming to pull a Spark image. You can cache the image that you want to use on a specified server to accelerate pod creation. For more information, see [Use an image cache CRD to accelerate pod creation]().



**Create a job template** 

Sample template:



    apiVersion: "sparkoperator.k8s.io/v1beta2"
    kind: SparkApplication
    metadata:
      name: spark-pi
      namespace: default
    spec:
      type: Scala
      mode: cluster
      image: "registry.aliyuncs.com/acs/spark-pi:ack-2.4.5-latest"
      imagePullPolicy: Always
      mainClass: org.apache.spark.examples.SparkPi
      mainApplicationFile: "local:///opt/spark/examples/jars/spark-examples_2.11-2.4.5.jar"
      sparkVersion: "2.4.5"
      restartPolicy:
        type: Never
      driver:
        cores: 2
        coreLimit: "2"
        memory: "3g"
        memoryOverhead: "1g"
        labels:
          version: 2.4.5
        serviceAccount: spark
        annotations:
          k8s.aliyun.com/eci-kube-proxy-enabled: 'true'
          k8s.aliyun.com/eci-image-cache: "true"
        tolerations:
        - key: "virtual-kubelet.io/provider"
          operator: "Exists"
      executor:
        cores: 2
        instances: 1
        memory: "3g"
        memoryOverhead: "1g"
        labels:
          version: 2.4.5
        annotations:
          k8s.aliyun.com/eci-kube-proxy-enabled: 'true'
          k8s.aliyun.com/eci-image-cache: "true"
        tolerations:
        - key: "virtual-kubelet.io/provider"
          operator: "Exists"



For more information about the fields, see https://github.com/GoogleCloudPlatform/spark-on-k8s-operator/blob/master/docs/api-docs.md. You can modify this template to create jobs. 



**Configure log collection and a Spark history server** 

Configure log collection

In the following example, the Spark stdout and stderr logs are collected. To collect the stdout and stderr logs, you can pass environment variables to the envVars field of the Spark drivers and executors to automate log collection, as shown in the following code block:




    envVars:
       aliyun_logs_project: liumi-k8s-spark
       aliyun_logs_machinegroup: k8s-group-app-spark
       aliyun_logs_test-stdout: stdout



You can automate log collection by setting the environment variables for the executors and drivers when you submit jobs. In the preceding code block, the stdout and stderr logs are collected and delivered to the test-stdout Logstore in the liumi-k8s-spark project of Log Service. 

For more information, see https://github.com/aliyuneci/Virtual-Kubelet-Example/blob/master/eci-sls/sls-config.yaml.

Configure a Spark history server

A Spark history server allows you to review Spark jobs. You can set the SparkConf field in the CRD of the Spark application to enable the application to sink events to OSS. Then, you can use the history server to retrieve the data from OSS.




    sparkConf:
       "spark.eventLog.enabled": "true"
       "spark.eventLog.dir": "oss://bigdatastore/spark-events"
       "spark.hadoop.fs.oss.impl": "org.apache.hadoop.fs.aliyun.oss.AliyunOSSFileSystem"
       # oss bucket endpoint such as oss-cn-beijing.aliyuncs.com
       "spark.hadoop.fs.oss.endpoint": "oss-cn-beijing.aliyuncs.com"
       "spark.hadoop.fs.oss.accessKeySecret": ""
       "spark.hadoop.fs.oss.accessKeyId": ""
            



Alibaba Cloud provides a Helm chart for you to deploy Spark history servers. To deploy a Spark history server, log on to the ACK console, navigate to the **Marketplace** \> **App Catalog** page, and then search for ack-spark-history-server. 



    oss:
      enableOSS: true
      # Please input your accessKeyId
      alibabaCloudAccessKeyId: ""
      # Please input your accessKeySecret
      alibabaCloudAccessKeySecret: ""
      # oss bucket endpoint such as oss-cn-beijing.aliyuncs.com
      alibabaCloudOSSEndpoint: "oss-cn-beijing.aliyuncs.com"
      # oss file path such as oss://bucket-name/path
      eventsDir: "oss://bigdatastore/spark-events"



After you deploy the Spark history server, you can view its external endpoint on the **Services** page. Then, you can access the external endpoint to view the history of Spark jobs. 



**View job results** 

1. Query the status of pods




    kubectl get pods
    NAME                            READY      STATUS     RESTARTS   AGE
    spark-pi-1547981232122-driver   1/1       Running    0          12s
    spark-pi-1547981232122-exec-1   1/1       Running    0          3s



2. Query the real-time data of Spark jobs in the web UI of Spark.




    kubectl port-forward spark-pi-1547981232122-driver 4040:4040



3. Query the status of the Spark application.




    ## Create a query task
    kubectl describe sparkapplication spark-pi
    ## The result returned for the query task  
    Name:         spark-pi
    Namespace:    default
    Labels:       <none>
    Annotations:  kubectl.kubernetes.io/last-applied-configuration:
                    {"apiVersion":"sparkoperator.k8s.io/v1alpha1","kind":"SparkApplication","metadata":{"annotations":{},"name":"spark-pi","namespace":"defaul...
    API Version:  sparkoperator.k8s.io/v1alpha1
    Kind:         SparkApplication
    Metadata:
      Creation Timestamp:  2019-01-20T10:47:08Z
      Generation:          1
      Resource Version:    4923532
      Self Link:           /apis/sparkoperator.k8s.io/v1alpha1/namespaces/default/sparkapplications/spark-pi
      UID:                 bbe7445c-1ca0-11e9-9ad4-062fd7c19a7b
    Spec:
      Deps:
      Driver:
        Core Limit:  200m
        Cores:       0.1
        Labels:
          Version:        2.4.0
        Memory:           512m
        Service Account:  spark
        Volume Mounts:
          Mount Path:  /tmp
          Name:        test-volume
      Executor:
        Cores:      1
        Instances:  1
        Labels:
          Version:  2.4.0
        Memory:     512m
        Volume Mounts:
          Mount Path:         /tmp
          Name:               test-volume
      Image:                  gcr.io/spark-operator/spark:v2.4.0
      Image Pull Policy:      Always
      Main Application File:  local:///opt/spark/examples/jars/spark-examples_2.11-2.4.0.jar
      Main Class:             org.apache.spark.examples.SparkPi
      Mode:                   cluster
      Restart Policy:
        Type:  Never
      Type:    Scala
      Volumes:
        Host Path:
          Path:  /tmp
          Type:  Directory
        Name:    test-volume
    Status:
      Application State:
        Error Message:
        State:          COMPLETED
      Driver Info:
        Pod Name:             spark-pi-driver
        Web UI Port:          31182
        Web UI Service Name:  spark-pi-ui-svc
      Execution Attempts:     1
      Executor State:
        Spark - Pi - 1547981232122 - Exec - 1:  COMPLETED
      Last Submission Attempt Time:             2019-01-20T10:47:14Z
      Spark Application Id:                     spark-application-1547981285779
      Submission Attempts:                      1
      Termination Time:                         2019-01-20T10:48:56Z
    Events:
      Type    Reason                     Age                 From            Message
      ----    ------                     ----                ----            -------
      Normal  SparkApplicationAdded      55m                 spark-operator  SparkApplication spark-pi was added, Enqueuing it for submission
      Normal  SparkApplicationSubmitted  55m                 spark-operator  SparkApplication spark-pi was submitted successfully
      Normal  SparkDriverPending         55m (x2 over 55m)   spark-operator  Driver spark-pi-driver is pending
      Normal  SparkExecutorPending       54m (x3 over 54m)   spark-operator  Executor spark-pi-1547981232122-exec-1 is pending
      Normal  SparkExecutorRunning       53m (x4 over 54m)   spark-operator  Executor spark-pi-1547981232122-exec-1 is running
      Normal  SparkDriverRunning         53m (x12 over 55m)  spark-operator  Driver spark-pi-driver is running
      Normal  SparkExecutorCompleted     53m (x2 over 53m)   spark-operator  Executor spark-pi-1547981232122-exec-1 completed



4. Query the results of Spark tasks




    NAME                                               READY     STATUS      RESTARTS   AGE
    spark-pi-1547981232122-driver   0/1       Completed   0          1m



If the Spark application is in the succeed state, or the Spark driver pod is in the completed state, the results of Spark jobs are available. You can query the log data to check the results of Spark jobs.




    kubectl logs spark-pi-1547981232122-driver
    Pi is roughly 3.152155760778804





FAQ 
------------------------

* What enhanced features are supported by the base image of Kubernetes Operator for Apache Spark that is provided by Alibaba Cloud and how do I enable these features?

  




The image of Kubernetes Operator for Apache Spark provided by Alibaba Cloud is optimized for Kubernetes, particularly for serverless Kubernetes clusters. Resource scheduling speed is 30 times faster and the startup speed of pods is increased by 50%. You can enable the enhanced features by setting the following variable in the Helm chart: enableAlibabaCloudFeatureGates: true. If you require a faster startup speed, you can set enableWebhook: false. 





