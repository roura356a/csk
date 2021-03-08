Spark应用 
============================



Spark on Kubernetes 
----------------------------------------

**使用方法：** 

**安装Spark Operator** 

Spark Operator是专门针对Spark on Kubernetes设计的operator，开发者可以通过使用CRD的方式，提交Spark任务到Kubernetes集群中。使用Spark Operator有如下三个重要的优势：

1. 弥补原生Spark对Kubernetes支持不足的部分。

2. 快速和Kubernetes生态中的存储、监控、日志等组件对接。

3. 支持故障恢复、弹性伸缩、调度优化等高级Kubernetes的特性。


**说明**

开发者可以阿里云容器服务Kubernetes的控制台的应用目录搜索 ack-spark-operator 进行安装。其中sparkJobNamespace是需要部署spark作业的命名空间，默认为default，置空字符串表示所有命名空间都监听。

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4020652951/p111889.png)

此外，Spark作业还需要一个ServiceAccount用来创建Pod的权限，开发者修改下面的serviceAccount、role、rolebinding的namespace，在所需的namespace下建立serviceAccount和权限绑定。




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





**构建作业镜像** 

编译Spark作业的Jar，使用Dockerfile进行镜像打包，以阿里云容器服务的spark基础镜像为例，设置Dockerfile内容如下：



    FROM registry.cn-hangzhou.aliyuncs.com/acs/spark:ack-2.4.5-latest
    RUN mkdir -p /opt/spark/jars
    # 如果需要使用OSS（读取OSS数据或者离线Event到OSS),可以添加下面的Jar到镜像中
    ADD https://repo1.maven.org/maven2/com/aliyun/odps/hadoop-fs-oss/3.3.8-public/hadoop-fs-oss-3.3.8-public.jar $SPARK_HOME/jars
    ADD https://repo1.maven.org/maven2/com/aliyun/oss/aliyun-sdk-oss/3.8.1/aliyun-sdk-oss-3.8.1.jar $SPARK_HOME/jars
    ADD https://repo1.maven.org/maven2/org/aspectj/aspectjweaver/1.9.5/aspectjweaver-1.9.5.jar $SPARK_HOME/jars
    ADD https://repo1.maven.org/maven2/org/jdom/jdom/1.1.3/jdom-1.1.3.jar $SPARK_HOME/jars
    COPY SparkExampleScala-assembly-0.1.jar /opt/spark/jars




**注意**

阿里云提供了Spark2.4.5的基础镜像，针对Kubernetes场景（调度、弹性）进行了优化，建议使用阿里云基础镜像。

**构建ImageCache进行镜像加速** 

Spark镜像的尺寸较大，拉取的数目会比较慢，可以通过ImageCache的方式实现镜像缓存启动加速。具体的制作流程参考：[https://help.aliyun.com/document_detail/141241.html](https://help.aliyun.com/document_detail/141241.html?spm=a2c4g.11186623.6.578.WkZlFF)



**编写作业模板** 

一个典型对作业模板如下：



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



更多字段的解释和信息可以参考\<a href="https://github.com/GoogleCloudPlatform/spark-on-k8s-operator/blob/master/docs/api-docs.md"\>文档\</a\>，默认开发者可以仿照模板进行修改。



**日志与历史服务器** 

日志采集配置

我们以spark的标准输出以及错误输出日志收集为例，可以在Spark driver和executor的envVars字段中注入环境变量，实现日志的自动采集。




    envVars:
       aliyun_logs_project: liumi-k8s-spark
       aliyun_logs_machinegroup: k8s-group-app-spark
       aliyun_logs_test-stdout: stdout



只需要提交作业的时候设置executor和driver的环境变量即可自动的日志收集，我们以上面的设置为例，语义是将该pod的标准输出、错误输出日志收集到liumi-k8s-spark这个sls项目的test-stdout日志仓库，效果如下：

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5020652951/p111912.png)更多的设置可以参考：https://github.com/aliyuneci/Virtual-Kubelet-Example/blob/master/eci-sls/sls-config.yaml

历史服务器：

历史服务器是审计Spark作业的最佳事件，在应用中可以通过在SparkApplicaiton的CRD中SparkConf字段的方式，将event写入到oss，再通过历史服务器读取oss的方式进行展现。




    sparkConf:
       "spark.eventLog.enabled": "true"
       "spark.eventLog.dir": "oss://bigdatastore/spark-events"
       "spark.hadoop.fs.oss.impl": "org.apache.hadoop.fs.aliyun.oss.AliyunOSSFileSystem"
       # oss bucket endpoint such as oss-cn-beijing.aliyuncs.com
       "spark.hadoop.fs.oss.endpoint": "oss-cn-beijing.aliyuncs.com"
       "spark.hadoop.fs.oss.accessKeySecret": ""
       "spark.hadoop.fs.oss.accessKeyId": ""
            



阿里云也提供了spark-history-server的Chart，开发者可以通过在容器服务控制台的 **市场** \> **应用目录** 中，搜索\`ack-spark-history-server\`进行安装。



![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0185613061/p174782.png)

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



安装完毕后，可以在集群详情页的 **服务** 中看到ack-spark-history-server的对外地址，访问对外地址即可查看历史任务归档。

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0185613061/p174784.png)![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6020652951/p111917.png)

**查看作业结果** 

1. 查看Pods的执行情况




    kubectl get pods
    NAME                            READY      STATUS     RESTARTS   AGE
    spark-pi-1547981232122-driver   1/1       Running    0          12s
    spark-pi-1547981232122-exec-1   1/1       Running    0          3s



2.查看实时spark ui




    kubectl port-forward spark-pi-1547981232122-driver 4040:4040



3.查看Spark App的状态




    ## 查询任务
    kubectl describe sparkapplication spark-pi
    ## 任务结果  
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



4.获取任务执行结果




    NAME                                               READY     STATUS      RESTARTS   AGE
    spark-pi-1547981232122-driver   0/1       Completed   0          1m



当sparkApplication的状态为succeed或者spark driver的Pod为completed的时候，可以查看日志获取结果。




    kubectl logs spark-pi-1547981232122-driver
    Pi is roughly 3.152155760778804





常见问题 
-------------------------

* 阿里云的Spark基础镜像提供了哪些能力增强，怎么开启。

  




阿里云的Spark基础镜像针对Spark on Kubernetes的场景进行了调度优化，尤其是针对serverless场景，调度速度提升30倍，启动速度提升50%。可以通过设置Helm Chart的变量中\`enableAlibabaCloudFeatureGates：true\`的方式开启，如果希望达到更快的启动速度，可以设置\`enableWebhook: false\`。

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6020652951/p111919.png)



