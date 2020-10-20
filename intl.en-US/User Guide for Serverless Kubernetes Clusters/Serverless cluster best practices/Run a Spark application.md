# Run a Spark application

## Background

**Benefits of running Spark on Kubernetes**

Compared with traditional deployment modes, for example, running Spark on YARN, running Spark on Kubernetes provides the following benefits:

1.  Resources are managed in a unified manner. All types of jobs can run in the same Kubernetes cluster. You do not need to separately create and manage a YARN cluster for running big data jobs.
2.  The infrastructure of a Kubernetes cluster is scalable. Kubernetes provides rich policies for you to scale resources and applications. You can scale Elastic Compute Service \(ECS\) instances, ECS Bare Metal Instances, or GPU instances based on the loads of applications. In addition, Kubernetes supports the collaboration with other services through tools such as Virtual Kubelet to further improve its scalability.
3.  Kubernetes allows you to easily isolate and restrict resources for complex distributed applications, freeing you from the cumbersome queue management and allocation in YARN clusters.
4.  Kubernetes has the advantage of containerization. To be specific, all applications are isolated from each other through containers. Each application can package its entire dependency stack in a Docker image and run in an independent environment. Even Spark of different versions can be deployed in the same Kubernetes cluster, without affecting each other.
5.  Kubernetes allows you to easily migrate big data applications to a cloud. Previously, you can migrate your big data applications to Alibaba Cloud by deploying a YARN cluster on ECS instances or creating an E-MapReduce cluster. Now you can use Container Service for Kubernetes to perform the migration. Container Service for Kubernetes grants you the full control over your Kubernetes clusters, without having to perform complex cluster management and O&M. Container Service for Kubernetes also allows you to enjoy rich cloud services featuring scalability and low costs.

In version 2.3.0, Spark provides a beta feature that allows you to deploy Spark on Kubernetes, apart from other deployment modes including standalone deployment, deployment on YARN, and deployment on Mesos. This feature has been enhanced continuously in subsequent releases. For more information, see [Running Spark on Kubernetes](https://spark.apache.org/docs/2.3.0/running-on-kubernetes.html?spm=a2c4g.11186623.2.15.13b42e14TyzdLS).

The following sections describe how to run a Spark application in a managed Kubernetes cluster, a serverless Kubernetes cluster, and a managed Kubernetes cluster with a virtual node backed by Elastic Container Instance \(ECI\), respectively.

## Run a Spark application in a managed Kubernetes cluster

**Prepare the data to be processed and the image of a Spark application**

For more information, see the following topics:

[Access data in Apsara File Storage for HDFS from ECI]()

[Access data in OSS from ECI]()

Create a managed Kubernetes cluster

If you have created a managed Kubernetes cluster in Container Service for Kubernetes, skip this step.

For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md).

**Submit the Spark application**

Create a role-based access control \(RBAC\) role for Spark.

Run the following command to create a service account in the default namespace:

```
kubectl create serviceaccount spark
```

Run the following command to assign a role to the service account:

```
kubectl create clusterrolebinding spark-role --clusterrole=edit --serviceaccount=default:spark --namespace=default
```

**Use spark-submit to submit the application \(not recommended\)**

```
liumihustdeMacBook-Pro:spark-on-k8s liumihust$ ./spark-2.3.0-bin-hadoop2.6/bin/spark-submit 
--master k8s://121.199.47.XX:6443 
--deploy-mode cluster 
--name WordCount 
--class com.aliyun.liumi.spark.example.WordCount 
--conf spark.kubernetes.authenticate.driver.serviceAccountName=spark 
--conf spark.executor.instances=2 
--conf spark.kubernetes.container.image=registry.cn-beijing.aliyuncs.com/liumi/spark:2.4.4-example 
local:///opt/spark/jars/SparkExampleJava-1.0-SNAPSHOT.jar
```

**Parameter description**

-master: the API server of the Kubernetes cluster. This parameter determines whether the Spark application is submitted to a Kubernetes cluster or a YARN cluster.

-deploy-mode: the deployment mode of the driver. Valid values: client and cluster. In client mode, the driver is deployed on the master node. In cluster mode, the driver is deployed on a worker node.

spark.executor.instances: the number of executors.

spark.kubernetes.container.image spark: the Spark image that contains the entire dependency stack, including the driver, executor, and application. You can also configure the image of each component separately.

**Process of submitting the application to the Kubernetes cluster**

![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4898161951/p112161.png)

1.  Spark creates a pod for running the driver in the Kubernetes cluster.
2.  After the driver pod starts, Spark calls the Kubernetes API to create pods for running executors, which are used to run jobs.
3.  After the jobs are completed, Kubernetes deletes the executor pods but retains the driver pod, which enters the Completed state. You can view logs of the jobs in the driver pod.
4.  The driver pod can only be deleted manually or by the Kubernetes garbage collector.

Results

The following figure shows the status of the driver and executor pods when the application was run.

In this example, it took about 20 minutes for the two executors, each of which is allocated 1 vCPU and 1 GB memory, to process the 30 GB data.

The results of the jobs are as follows:

```
 [root@liumi-hdfs ~]# $HADOOP_HOME/bin/hadoop fs -cat /pod/data/A-Game-of-Thrones-Result/*
(142400000,the)
(78400000,and)
(77120000,)
(62200000,to)
(56690000,of)
(56120000,a)
(43540000,his)
(35160000,was)
(30480000,he)
(29060000,in)
(26640000,had)
(26200000,her)
(23050000,as)
(22210000,with)
(20450000,The)
(19260000,you)
(18300000,I)
(17510000,she)
(16960000,that)
(16450000,He)
(16090000,not)
(15980000,it)
(15080000,at)
(14710000,for)
(14410000,on)
(12660000,but)
(12470000,him)
(12070000,is)
(11240000,from)
(10300000,my)
(10280000,have)
(10010000,were)
```

You have deployed and run a Spark application in the Kubernetes cluster. You can submit more Spark applications to the Kubernetes cluster.

## Run a Spark application in a serverless Kubernetes cluster

Compared with a managed Kubernetes cluster, a serverless Kubernetes cluster does not require you to reserve any resources before you submit an application or scale resources for the cluster. A serverless Kubernetes cluster automatically applies for resources when you submit an application, and releases the resources after the application is run. After the application is run, only SparkApplication, which is a Custom Resource Definition \(CRD\), and a driver pod in the Completed state are retained. The driver pod only stores the management and control data.

The following figure shows how a Spark application is run in a serverless Kubernetes cluster.

![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4898161951/p112182.png)

Serverless Kubernetes uses Virtual Kubelet to schedule pods to ECI. Although the service architecture of Serverless Kubernetes is significantly different from that of Container Service for Kubernetes, these two services are both fully compatible with standard Kubernetes. Therefore, to run a Spark application in a Serverless Kubernetes cluster, you need to prepare the same data for running the Spark application in a managed Kubernetes cluster. Specifically, you need to prepare data in Apsara File Storage for HDFS and prepare images such as the Spark base image and the image of the Spark application. The differences between these two modes of running a Spark application lie in the method of submitting the application and the basic environment configurations.

**Create a serverless Kubernetes cluster**

For more information, see [Create a serverless Kubernetes cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md).

In all the examples provided in this topic, you need to copy kubeconfig files to the local computer and use the files to access the Kubernetes clusters.

Log on to the Container Service console. On the Clusters page, click Create Kubernetes Cluster in the upper-right corner. In the dialog box that appears, select Standard Serverless Cluster.

![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4898161951/p112193.png)

Parameter configuration

1.  Cluster Name: the custom name of the serverless Kubernetes cluster.
2.  Region and Zone: the region and zone in which the cluster is to be deployed.
3.  VPC: the Virtual Private Cloud \(VPC\) in which the cluster is to be deployed. You can create a new VPC or specify an existing VPC.
4.  Public Access: specifies whether to allow access to the API server of the cluster from the Internet. Select the Expose API Server with EIP checkbox if you need to access the API server from the Internet.
5.  PrivateZone: specifies whether to enable the PrivateZone service for the cluster. PrivateZone allows access to services in the VPC through their domain names. This service must be enabled for the cluster.
6.  Log Service: specifies whether to collect logs to Log Service. We recommend that you enable Log Service for the cluster.

![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5898161951/p112192.png)

**Note:**

1. Before you submit the Spark application, upgrade Virtual Kubelet to the latest version in the serverless Kubernetes cluster. Otherwise, you cannot run Spark jobs on a virtual node. If the cluster is newly created, the upgrade is not required.

2. Serverless Kubernetes uses PrivateZone to expose services. You must enable the PrivateZone service for the serverless Kubernetes cluster when you create the cluster. If the PrivateZone service is not enabled when you create the cluster, contact Alibaba Cloud technical support to enable the service for you. Otherwise, Spark executors cannot access the driver service.

**Create an image cache**

To accelerate pod creation in case that you need to create a large number of pods at a time, you can cache the image of the Spark application on ECIs locally. You can use a standard Kubernetes CRD to create an image cache. For more information, see [Use an image cache CRD to accelerate pod creation]().

Submit the Spark application

Currently, spark-submit only supports a few parameters. We recommend that you use Kubernetes Operator for Apache Spark instead of spark-submit to submit a Spark application to a serverless Kubernetes cluster.

[Kubernetes Operator for Apache Spark](https://github.com/GoogleCloudPlatform/spark-on-k8s-operator?spm=a2c4g.11186623.2.26.13b42e14TyzdLS) is designed to deploy and maintain Spark applications in Kubernetes clusters.

![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5898161951/p112185.png)

The operator consists of the following components:

**SparkApplication**: the controller for the standard Kubernetes CRD SparkApplication. The controller watches events of creation, updates, and deletion of SparkApplication objects and acts on the watch events.

**ScheduledSparkApplication**: the upgraded version of SparkApplication. It supports submitting jobs with custom scheduling configurations, for example, cron jobs.

**Submission runner**: runs spark-submit for submissions received from the controller.

**Spark pod monitor**: watches for Spark pods and sends updates of pod status to the controller.

Install Kubernetes Operator for Apache Spark

We recommend that you use [Helm 3](https://helm.sh/blog/helm-3-released/?spm=a2c4g.11186623.2.28.13b42e14TyzdLS) to install the operator.

```
helm repo add incubator http://storage.googleapis.com/kubernetes-charts-incubator
helm install incubator/sparkoperator --namespace default  --set operatorImageName=registry.cn-hangzhou.aliyuncs.com/eci_open/spark-operator  --set operatorVersion=v1beta2-1.0.1-2.4.4  --generate-name --set enableWebhook=true
```

**Note:** Do not set enableWebhook to true when you install the operator in a serverless Kubernetes cluster.

After the operator is installed, a pod for the operator is created in the cluster.

Options of the installation command:

1.  -set operatorImageName: the image of the operator. ECI cannot directly pull the default image from Google. You can pull the image to the local computer and then push it to Alibaba Cloud Container Registry.
2.  -set operatorVersion: the version of the operator. Specify the version separately. Do not write the name of the image repository and version together.
3.  -generate-name: the name of the operator. You do not need to explicitly specify a name.
4.  -set enableWebhook: specifies whether to enable the webhook. By default, the webhook is disabled. If you need to run a Spark application in a managed Kubernetes cluster with a virtual node backed by ECI, advanced features such as nodeSelector and tolerations will be used. In this case, you need to enable the webhook. Do not enable the webhook for a serverless Kubernetes cluster.

**Note:** When you install Kubernetes Operator for Apache Spark, make sure that the image of the operator can be pulled to ECI. This is because the same image is required to start a job for deleting data when you uninstall the operator. If the image cannot be pulled, the job will fail. In this case, you have to manually delete all data. We recommend that you use the image that we provide in Alibaba Cloud Container Registry.

Configuration file of the wordcount application:

```
apiVersion: "sparkoperator.k8s.io/v1beta2"
kind: SparkApplication
metadata:
  name: wordcount
  namespace: default
spec:
  type: Java
  mode: cluster
  image: "registry.cn-beijing.aliyuncs.com/liumi/spark:2.4.4-example"
  imagePullPolicy: IfNotPresent
  mainClass: com.aliyun.liumi.spark.example.WordCount
  mainApplicationFile: "local:///opt/spark/jars/SparkExampleJava-1.0-SNAPSHOT.jar"
  sparkVersion: "2.4.4"
  restartPolicy:
    type: OnFailure
    onFailureRetries: 2
    onFailureRetryInterval: 5
    onSubmissionFailureRetries: 2
    onSubmissionFailureRetryInterval: 10
  timeToLiveSeconds: 36000
  sparkConf:
    "spark.kubernetes.allocation.batch.size": "10"
  driver:
    cores: 2
    memory: "4096m"
    labels:
      version: 2.4.4
      spark-app: spark-wordcount
      role: driver
    annotations:
      k8s.aliyun.com/eci-image-cache: "true"
    serviceAccount: spark
  executor:
    cores: 1
    instances: 100
    memory: "1024m"
    labels:
      version: 2.4.4
      role: executor
    annotations:
      k8s.aliyun.com/eci-image-cache: "true"
```

**Note:** Most parameters of the Spark application can be set through the fields supported by the SparkApplication CRD. For more information about the supported fields, see [SparkApplication CRD](https://github.com/GoogleCloudPlatform/spark-on-k8s-operator/blob/v1beta2-1.0.1-2.4.4/manifest/crds/sparkoperator.k8s.io_sparkapplications.yaml?spm=a2c4g.11186623.2.30.13b42e14TyzdLS&file=sparkoperator.k8s.io_sparkapplications.yaml). You can also set the parameters through a sparkConf file.

**Run the following command to submit the application:**

```
kubectl create -f wordcount-operator-example.yaml
```

Results

In this example, 100 executors, each of which is allocated 1 vCPU and 1 GB memory, were created concurrently. The size of the application image is about 500 MB.

The following figure shows the status of the executor pods when the application was run.

As shown in the preceding figure, all the 100 executor pods were started within 30 seconds, and 93 of them were started within 20 seconds.

Check the total time taken to run the application, including the time taken to schedule the 100 executor pods to ECI, the time taken to prepare resources for each executor pod, and the actual running time of the application.

```
exitCode: 0
finishedAt: '2019-11-16T07:31:59Z'
reason: Completed
startedAt: '2019-11-16T07:29:01Z'
```

According to the logs, the total time is 178 seconds, which is greatly reduced compared with that taken to run the Spark application in a managed Kubernetes cluster.

**Run a Spark application in a managed Kubernetes cluster with a virtual node backed by ECI**

The driver and executors of a Spark application are started in a serial manner. In the preceding example, ECI shows its powerful capability in concurrently creating a large number of executor pods. However, in a Serverless Kubernetes cluster, it also takes about 20 seconds to schedule the driver pod to ECI. That is, the cluster needs to wait about 20 seconds before it starts to create executor pods. For applications that requires fast response, the time taken to start the driver pod may matter more than the time consumed by the executor pods to run jobs. In this case, we recommend that you run a Spark application in a managed Kubernetes cluster with a virtual node backed by ECI, as shown in the following figure.

![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6898161951/p112188.png)

To allow a managed Kubernetes cluster to schedule executor pods to a virtual node backed by ECI, follow these steps:

1. Install Virtual Kubelet in the managed Kubernetes cluster.

Log on to the Container Service console. In the left-side navigation pane, choose Marketplace \> App Catlog. On the page that appears, enter ack-virtual-node in the search box in the upper-right corner and press Enter.

Click ack-virtual-node. On the page that appears, select the target cluster from the Cluster drop-down list in the right-side pane.

The following code provides an example of how to configure the required parameters:

```
virtualNode:
  image:
    repository: registry.cn-hangzhou.aliyuncs.com/acs/virtual-nodes-eci
    tag: v1.0.0.1-aliyun
affinityAdminssion:
  enabled: true 
  image:
    repository: registry.cn-hangzhou.aliyuncs.com/ask/virtual-node-affinity-admission-controller
    tag: latest
env:
  ECI_REGION: "cn-hangzhou" # The region where the cluster resides.
  ECI_VPC: vpc-bp187fy2e7l123456 # The VPC where the cluster resides. Specify the VPC that is selected when you create the cluster. You can view the VPC information on the details page of the cluster.
  ECI_VSWITCH: vsw-bp1bqf53ba123456 # The VSwitch used to create ECIs. Specify the VSwitch that is selected when you create the cluster. You can view the VSwitch information on the details page of the cluster.
  ECI_SECURITY_GROUP: sg-bp12ujq5zp12346 # The security group used to create ECIs. Specify the security group that is selected when you create the cluster. You can view the security group information on the details page of the cluster.
  ECI_ACCESS_KEY: XXXXX # The AccessKey ID of the account used to access ECI.
  ECI_SECRET_KEY: XXXXX # The AccessKey secret of the account used to access ECI.
  ALIYUN_CLUSTERID: virtual-kubelet
```

**2. Modify the YAML configuration file of the Spark application.**

Add the following fields to the executor field in the configuration file:

```
nodeSelector:
  type: virtual-kubelet
tolerations:
  - key: virtual-kubelet.io/provider
    operator: Exists
```

The complete configuration file is as follows:

```
apiVersion: "sparkoperator.k8s.io/v1beta2"
kind: SparkApplication
metadata:
  name: wordcount
  namespace: default
spec:
  type: Java
  mode: cluster
  image: "registry.cn-beijing.aliyuncs.com/liumi/spark:2.4.4-example"
  imagePullPolicy: IfNotPresent
  mainClass: com.aliyun.liumi.spark.example.WordCount
  mainApplicationFile: "local:///opt/spark/jars/SparkExampleJava-1.0-SNAPSHOT.jar"
  sparkVersion: "2.4.4"
  restartPolicy:
    type: OnFailure
    onFailureRetries: 2
    onFailureRetryInterval: 5
    onSubmissionFailureRetries: 2
    onSubmissionFailureRetryInterval: 10
  timeToLiveSeconds: 36000
  sparkConf:
    "spark.kubernetes.allocation.batch.size": "10"
  driver:
    cores: 2
    memory: "4096m"
    labels:
      version: 2.4.4
      spark-app: spark-wordcount
      role: driver
    annotations:
      k8s.aliyun.com/eci-image-cache: "true"
    serviceAccount: spark
  executor:
    cores: 1
    instances: 100
    memory: "1024m"
    labels:
      version: 2.4.4
      role: executor
    annotations:
      k8s.aliyun.com/eci-image-cache: "true"
    #nodeName: virtual-kubelet
    nodeSelector:
      type: virtual-kubelet
    tolerations:
    - key: virtual-kubelet.io/provider
      operator: Exists
```

By using the preceding configuration file, you can schedule the driver pod inside the managed Kubernetes cluster and executor pods to ECI.

3. Submit the application.

The following figure shows the status of the executor pods when the application was run.

Check the total time taken to run the application.

```
exitCode: 0
finishedAt: '2019-11-16T07:25:05Z'
reason: Completed
startedAt: '2019-11-16T07:22:40Z'
```

According to the logs, the total time is 145 seconds. More importantly, the driver pod was scheduled to a work node in the managed Kubernetes cluster, which took only 2 seconds.

## Appendix

**Spark base image**

This topic uses the image provided by Google, that is, gcr.io/spark-operator/spark:v2.4.4.

We have pulled the image from Google Container Registry and pushed it to Alibaba Cloud Container Registry. The paths for pulling the image from Alibaba Cloud Container Registry are as follows:

Internet :registry.\{ Region ID\}.aliyuncs.com/eci\_open/spark:2.4.4.

VPC: registry-vpc.\{ Region ID\}.aliyuncs.com/eci\_open/spark:2.4.4.

**Image of Kubernetes Operator for Apache Spark**

This topic uses the image provided by Google, that is, gcr.io/spark-operator/spark-operator:v1beta2-1.0.1-2.4.4.

We have pulled the image from Google Container Registry and pushed it to Alibaba Cloud Container Registry. The paths for pulling the image from Alibaba Cloud Container Registry are as follows:

Internet :registry.\{ Region ID\}.aliyuncs.com/eci\_open/spark-operator:v1beta2-1.0.1-2.4.4.

VPC: registry-vpc.\{ Region ID\}.aliyuncs.com/eci\_open/spark-operator:v1beta2-1.0.1-2.4.4.

