# A solution to log collection problems of Kubernetes clusters by using log-pilot, Elasticsearch, and Kibana {#concept_bmg_fkb_wdb .concept}

Requirements for logs of distributed Kubernetes clusters always bother developers. This is mainly because of the characteristics of containers and the defects of log collection tools.

-   Characteristics of containers:
    -   Many collection targets: The characteristics of containers cause the number of collection targets is large, which requires to collect the container logs and container stdout.  Currently, no good tool can collect file logs from containers dynamically.  Different data sources have different collection softwares. However, no one-stop collection tool exists.
    -   Difficulty caused by auto scaling: Kubernetes clusters are in the distributed mode. The auto scaling of services and the environment brings great difficulty to log collection. You cannot configure the log collection path in advance, the same as what you do in the traditional virtual machine \(VM\) environment. The dynamic collection and data integrity are great challenges.
-   Defects of current log collection tools:
    -   Lack the capability to dynamically configure log collection:  The current log collection tools require you to manually configure the log collection method and path in advance. These tools cannot dynamically configure the log collection because they cannot automatically detect the lifecycle changes or dynamic migration of containers.
    -   Log collection problems such as logs are duplicate or lost:  Some of the current log collection tools collect logs by using the tail method. Logs may be lost in this way. For example, the application is writing logs when the log collection tool is being restarted. Logs written during this period may be lost. Generally, the conservative solution is to collect logs of 1 MB or 2 MB previous to the current log by default. However, this may cause the duplicate log collection.
    -   Log sources without clear marks:  An application may have multiple containers that output the same application logs. After all the application logs are collected to a unified log storage backend, you cannot know a log is generated on which application container of which node when querying logs.

This document introduces log-pilot, a tool to collect Docker logs, and uses the tool together with Elasticsearch and Kibana to provide a one-stop solution to log collection problems in the Kubernetes environment.

## Introduction on log-pilot {#section_eys_gkb_wdb .section}

Log-pilot is an intelligent tool used to collect container logs, which not only collects container logs and outputs these logs to multiple types of log storage backends efficiently and conveniently, but also dynamically discovers and collects log files from containers.

Log-pilot uses declarative configuration to manage container events strongly and obtain the stdout and file logs of containers, which solves the problem of auto scaling. Besides, log-pilot has the functions of automatic discovery, maintenance of checkpoint and handle, and automatic tagging for log data, which effectively deals with the problems such as dynamic configuration, duplicate logs, lost logs, and log source marking.

Currently, log-pilot is completely open-source in GitHub. The project address is [https://github.com/AliyunContainerService/log-pilot](https://github.com/AliyunContainerService/log-pilot).  You can know more implementation principles about it.

## Declarative configuration for container logs {#section_fys_gkb_wdb .section}

Log-pilot supports managing container events, can dynamically listen to the event changes of containers, parse the changes according to the container labels, generate the configuration file of log collection, and then provide the file to collection plug-in to collect logs.

For Kubernetes clusters, log-pilot can dynamically generate the configuration file of log collection according to the environment variable `aliyun_logs_$name = $path`. This environment variable contains the following two variables:

-   One variable is $name, a custom string which indicates different meanings in different scenarios. In this scenario, $name indicates index when collecting logs to Elasticsearch.
-   The other is $path which supports two input modes, stdout and paths of log files within containers, respectively corresponding to the standard output of logs and log files within containers.
    -   Stdout indicates to collect standard output logs from containers. In this example, to collect Tomcat container logs, configure the label `aliyun.logs.catalina=stdout` to collect standard output logs of Tomcat.
    -   The path of a log file within a container also supports wildcards. To collect logs within the Tomcat container, configure the environment variable `aliyun_logs_access=/usr/local/tomcat/logs/*.log`.  To not use the keyword aliyun, you can use the environment variable PILOT\_LOG\_PREFIX, which is also provided by log-pilot, to specify the prefix of your declarative log configuration. For example, `PILOT_LOG_PREFIX: "aliyun,custom"`.

Besides, log-pilot supports multiple log parsing formats, including none, JSON, CSV, Nginx, apache2, and regxp. You can use the `aliyun_logs_$name_format=<format>` label to tell log-pilot to use what format to parse logs when collecting logs.

Log-pilot also supports custom tags. If you configure `aliyun_logs_$name_tags="K1=V1,K2=V2"` in the environment variable, K1=V1 and K2=V2 are collected to log output of the container during the log collection.  Custom tags help you tag the log generation environment for convenient statistics, routing, and filter of logs.

## Log collection mode {#section_iys_gkb_wdb .section}

In this document, deploy a log-pilot on each machine and collect all the Docker application logs from the machines.

Compared with deploying a logging container on each pod, the most obvious advantage of this solution is less occupied resources. The larger the cluster scale is, the more obvious the advantage is. This solution is also recommended in the community.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6945/15395858064695_en-US.png)

## Prerequisites {#section_kys_gkb_wdb .section}

You have activated Container Service and created a Kubernetes cluster.  In this example, create a Kubernetes cluster in China East 1 \(Hangzhou\).

## Step 1 Deploy Elasticsearch {#section_xml_n11_12b .section}

1.  Connect to your Kubernetes cluster.  For more information, see [EN-US\_TP\_6880.md\#](reseller.en-US/User Guide/Kubernetes cluster/Clusters/Create a cluster.md#) or [EN-US\_TP\_6882.md\#](reseller.en-US/User Guide/Kubernetes cluster/Clusters/Access Kubernetes clusters by using SSH.md#).
2.  Deploy the resource object related to Elasticsearch first. Then, enter the following orchestration template. This orchestration template includes an elasticsearch-api service, an elasticsearch-discovery service, and a status set of Elasticsearch. All of these objects are deployed under the namespace kube-system.

    ```
    kubectl apply -f https://acs-logging.oss-cn-hangzhou.aliyuncs.com/elasticsearch.yml
    ```

3.  After the successful deployment, corresponding objects are under the namespace kube-system. Run the following commands to check the running status:

    ```
    $ kubectl get svc,StatefulSet -n=kube-system
     NAME TYPE CLUSTER-IP EXTERNAL-IP PORT(S) AGE
     svc/elasticsearch-api ClusterIP 172.21.5.134 <none> 9200/TCP 22h
     svc/elasticsearch-discovery ClusterIP 172.21.13.91 <none> 9300/TCP 22h
     ...
     NAME DESIRED CURRENT AGE
     statefulsets/elasticsearch 3 3 22h
    ```


## Step 2 Deploy log-pilot and the Kibana service {#section_fgg_511_12b .section}

1.  Deploy the log-pilot log collection tool. The orchestration template is as follows:

    ```
    kubectl apply -f https://acs-logging.oss-cn-hangzhou.aliyuncs.com/log-pilot.yml
    ```

2.  Deploy the Kibana service. The sample orchestration template contains a service and a deployment.

    ```
    kubectl apply -f https://acs-logging.oss-cn-hangzhou.aliyuncs.com/kibana.yml
    ```


## Step 3 Deploy the test application Tomcat {#section_nbt_gkb_wdb .section}

After deploying the log tool set of Elasticsearch + log-pilot + Kibana, deploy a test application Tomcat to test whether or not logs can be successfully collected, indexed, and displayed.

The orchestration template is as follows:

```
apiVersion: v1
kind: Pod
metadata:
  name: tomcat
  namespace: default
  labels:
    name: tomcat
spec:
  containers:
  - image: tomcat
    name: tomcat-test
    volumeMounts:
    - mountPath: /usr/local/tomcat/logs
      name: accesslogs
    env:
     - name: aliyun_logs_catalina                                 
       value: "stdout" ##Collect standard output logs.
     - name: aliyun_logs_access
       value: "/usr/local/tomcat/logs/catalina. *.log" ## Collect log files within the container. 
  volumes:
    - name: accesslogs
      emptyDir: {}
```

The Tomcat image is a Docker image that both uses stdout and file logs.   In the preceding orchestration, the log collection configuration file is dynamically generated by defining the environment variable in the pod. See the following descriptions for the environment variable:

-   `aliyun_logs_catalina=stdout` indicates to collect stdout logs from the container.
-   `aliyun_logs_access=/usr/local/tomcat/logs/catalina. *.log` indicates to collect all the log files whose name matches catalina. \*.log under the directory /usr/local/tomcat/logs/  from the container. 

In the Elasticsearch scenario of this solution, the `$name` in the environment variable indicates index. In this example, `$name` is catalina and access.

## Step 4 Expose the Kibana service to Internet {#section_wbt_gkb_wdb .section}

The Kibana service deployed in the preceding section is of the NodePort type, which cannot be accessed from the Internet by default. Therefore, create an Ingress in this document to access the Kibana service from Internet and test whether or not logs are successfully indexed and displayed.

1.  Create an Ingress to access the Kibana service from Internet.  In this example, use the simple routing service to create an Ingress. For more information, see [EN-US\_TP\_6932.md\#](reseller.en-US/User Guide/Kubernetes cluster/Server Load Balancer/Support for Ingress.md#).  The orchestration template of the Ingress is as follows:

    ```
    apiVersion: extensions/v1beta1
     kind: Ingress
     metadata:
       name: kibana-ingress
       namespace: kube-system #Make sure the namespace is the same as that of the Kibana service.
     spec:
       rules:
       - http:
           paths:
           - path: /
             backend:
               serviceName: kibana #Enter the name of the Kibana service.
               servicePort: 80 #Enter the port exposed by the Kibana service.
    ```

2.  After the Ingress is successfully created, run the following commands to obtain the access address of the Ingress:

    ```
    $ kubectl get ingress -n=kube-system
     NAME HOSTS ADDRESS PORTS AGE
     shared-dns * 120.55.150.30 80 5m
    ```

3.  Access the address in the browser as follows.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6945/15395858064696_en-US.png)

4.  Click **Management** in the left-side navigation pane. Then, click **Index Patterns** \> **Create Index Pattern**.  The detailed index name is the `$name` variable suffixed with a time string. You can create an index pattern by using the wildcard `*`.   In this example, use `$name*` to create an index pattern.

    You can also run the following commands to enter the corresponding pod of Elasticsearch and list all the indexes of Elasticsearch:

    ```
    $ kubectl get pods -n=kube-system #Find the corresponding pod of Elasticsearch.
       ...
    $ kubectl exec -it elasticsearch-1 bash #Enter a pod of Elasticsearch.
     ...
    $ curl 'localhost:9200/_cat/indices? v' ## List all the indexes.
     health status index uuid pri rep docs.count docs.deleted store.size pri.store.size
     green open .kibana x06jj19PS4Cim6Ajo51PWg 1 1 4 0 53.6kb 26.8kb
     green open access-2018.03.19 txd3tG-NR6-guqmMEKKzEw 5 1 143 0 823.5kb 411.7kb
     green open catalina-2018.03.19 ZgtWd16FQ7qqJNNWXxFPcQ 5 1 143 0 915.5kb 457.5kb
    ```

5.  After successfully creating the indexes, click **Discover** in the left-side navigation pane, select the created index and the corresponding time range, and then enter the related field in the search box to query logs.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6945/15395858064697_en-US.png)


Then, you have successfully tested the solution to log collection problems of Alibaba Cloud Kubernetes clusters based on log-pilot, Elasticsearch, and Kibana. By using this solution, you can deal with requirements for logs of distributed Kubernetes clusters effectively, improve the Operation and Maintenance and operational efficiencies, and guarantee the continuous and stable running of the system.

