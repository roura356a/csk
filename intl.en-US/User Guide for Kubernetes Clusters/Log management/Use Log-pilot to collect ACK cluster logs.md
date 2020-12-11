---
keyword: [Log-pilot, log collection, Elasticsearch, K8s]
---

# Use Log-pilot to collect ACK cluster logs

This topic describes how to use Log-pilot with Elasticsearch and Kibana to provide an all-in-one solution for collecting logs from Kubernetes clusters. Log-pilot is an open source Docker log tool.

-   Container Service for Kubernetes \(ACK\) is activated and an ACK cluster is created. For more information, see [Create a managed kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md).

    In this example, an ACK cluster is created in the China \(Hangzhou\) region.

-   Make sure that the created ACK cluster can connect to the network where an Elasticsearch cluster is deployed.

When developers collect logs from Kubernetes distributed clusters, they must face the following challenges:

-   Challenges from the container side:
    -   The developers need to collect a large number of log files, such as container log files and stdout files. However, current log tools cannot be used to dynamically discover and collect logs inside containers. Each type of logs can be collected by only the related log tool. No log tool is available to collect all types of log.
    -   Kubernetes clusters are scalable and require a more dynamic method to collect logs. A Kubernetes cluster is built on a distributed architecture. The service and runtime of a Kubernetes cluster can be scaled in or out based on actual requirements. Therefore, log collection in Kubernetes clusters is more complicated than that in virtual machines where you can collect logs from specified paths. A dynamic and all-in-one log collection solution is required for Kubernetes clusters.
-   Challenges from the log tool side:
    -   Current log tools do not support dynamic configurations. These log tools collect logs based on predefined settings and paths. When containers move through their lifecycles or when they are scaled, these tools cannot update the log collection settings or paths accordingly. As a result, log collection may fail.
    -   Some log tools may cause duplicate log collections or data loss. Some log tools use the --tail option of the Docker log collection command to collect logs. This may cause data loss. For example, a log tool does not collect log data when it restarts. As a result, the log data generated within this period of time is lost. The conventional way to resolve this issue is to collect log data that is generated one or two minutes earlier than the current time. As a result, the log tool may collect logs that have been collected before.
    -   The sources of the collected logs are unknown. An application may have multiple containers that print the same application logs. After all the application logs are collected and stored in your log storage, you may find that the logs have the same content. As a result, you cannot identify the containers that generate the logs and the nodes where the containers run.

## Introduction to Log-pilot

Log-pilot is an intelligent container log collection tool. Log-pilot can efficiently collect and import logs to multiple types of log storage. In addition, Log-pilot can dynamically discover and collect logs inside containers.

Log-pilot uses declarative configuration to define container events for collecting stdout files and log files inside containers. This allows you to collect logs when containers are scaled. In addition, Log-pilot supports automatic discovery, checkpoints, file handle holding, and automatic tagging for log data. These features can efficiently deal with log collection problems, such as dynamic collection settings, duplicate log collections, data loss, and log source annotation.

Log-pilot is a completely open source project on Github. For more information, visit [github.com/AliyunContainerService/log-pilot](https://github.com/AliyunContainerService/log-pilot).

## Declarative configuration for container log collection

Log-pilot supports container event management. Log-pilot dynamically listens to changes of containers, parses the changes based on the environment variables of containers, and generates a configuration file for log collection. Then, the log collection plug-in uses the configuration file to collect logs.

In a Kubernetes cluster, Log-pilot dynamically generates a configuration file for log collection based on the `aliyun_logs_$name = $path` environment variable of a container. The environment variable consists of two parameters:

-   $name. This parameter can be set to a custom string to meet the requirements in different scenarios. In this scenario, logs are collected to an Elasticsearch cluster. Therefore, the value of $name indicates the index that stores the log data in the Elasticsearch cluster.
-   $path. This parameter can be set to stdout or a path. A value of stdout indicates the stdout files. A value of a path indicates the log files inside a container.
    -   If the value is set to stdout, stdout files are collected. In this example, set the environment variable to `aliyun.logs.catalina=stdout` to collect stdout files from an Apache Tomcat container.
    -   If the value is set to a path, log files in the specified path are collected. Wildcard characters can be used in the value of the path. In this example, set the environment variable to `aliyun_logs_access=/usr/local/tomcat/logs/*.log` to collect logs files inside the Apache Tomcat container If you want to use a prefix other than aliyun, Log-pilot provides the PILOT\_LOG\_PREFIX environment variable for you to customize the prefix of the declarative configuration, such as `PILOT_LOG_PREFIX: "aliyun,custom"`.

In addition, Log-pilot can parse logs in a variety of formats. You can use the `aliyun_logs_$name_format=<format>` environment variable to specify the formats of the collected logs. The supported formats are: none, json, csv, nginx, apache2, and regxp.

Log-pilot also allows you to add custom tags to the collected logs. For example, you can specify `aliyun_logs_$name_tags="K1=V1,K2=V2"` in the environment variable. This way, the collected logs are tagged with K1=V1 and K2=V2. You can use custom tags to find the environment that generates logs with certain tags. This makes log data aggregation, log routing, and log filtering more convenient for you.

## Log collection mode

In this topic, Log-pilot is deployed on all nodes in a cluster to collect logs of all containers that are deployed on each node.

Compared with the mode where a logging container is deployed on each pod, this mode consumes less resources. This mode saves more resources in a larger cluster. This mode is also recommended by the GitHub community.

![Log collection mode](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2341258951/p10636.png)

## Step 1: Create an Elasticsearch cluster

**Note:** The Elasticsearch cluster version must be earlier than V7.X.X. For more information about how to create an Elasticsearch cluster, see [Create an Alibaba Cloud Elasticsearch cluster](/intl.en-US/Quick Start/Step 1: Create a cluster/Create an Alibaba Cloud Elasticsearch cluster.md). By default, the Alibaba Cloud Elasticsearch cluster does not automatically create indexes for documents that are not indexed. However, when Log-pilot automatically collects container logs, it must create indexes for documents based on the log collection configuration. Therefore, you must enable the **auto indexing** feature.

Perform the following steps to enable the **auto indexing** feature:

1.  Log on to the [Alibaba Cloud Elasticsearch console](https://elasticsearch.console.aliyun.com/#/instances).

2.  In the left-side navigation pane, click Elasticsearch Clusters. On the Clusters page, click the hyperlink of the created cluster in the **Cluster ID/Name** column.

3.  In the left-side navigation pane, click **Cluster Configuration** to go to the **Cluster Configuration** page. In the YML Configuration section, click **Modify Configuration** on the right side of the page. The YML File Configuration panel appears.

4.  Set Auto Indexing to **Enable**. Select **This operation will restart the cluster. Continue?**and click **OK**.


## Step 2: Deploy the Log-pilot component

To reduce the consumption on node resources, Log-pilot is deployed as a DaemonSet on each node of the created ACK cluster.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Workload**.

5.  Select **kube-system** from the **Namespace** drop-down list.

6.  In the upper-right corner of the **Deployments** tab, click **Create from Template** to go to the Create from Template page.

7.  Select **Custom** from the **Sample Template** drop-down list, paste the following content to the **template**, and then click **Create**.

    ```
    apiVersion: apps/v1
    kind: DaemonSet
    metadata:
      name: log-pilot
      labels:
        app: log-pilot
      # Specify the namespace to which the Deployment belongs.
      namespace: kube-system
    spec:
      selector:
        matchLabels:
          app: log-pilot
      updateStrategy:
        type: RollingUpdate
      template:
        metadata:
          labels:
            app: log-pilot
          annotations:
            scheduler.alpha.kubernetes.io/critical-pod: ''
        spec:
          # Specify a toleration to allow pods to be scheduled to nodes with matching taints.
          tolerations:
          - key: node-role.kubernetes.io/master
            effect: NoSchedule
          containers:
          - name: log-pilot
            # For more information about Log-pilot versions, visit https://github.com/AliyunContainerService/log-pilot/releases.
            image: registry.cn-hangzhou.aliyuncs.com/acs/log-pilot:0.9.6-filebeat
            resources:
              limits:
                memory: 500Mi
              requests:
                cpu: 200m
                memory: 200Mi
            env:
              - name: "NODE_NAME"
                valueFrom:
                  fieldRef:
                    fieldPath: spec.nodeName
              - name: "LOGGING_OUTPUT"
                value: "elasticsearch"
              # Make sure that the ACK cluster can connect to the network where the Elasticsearch cluster is deployed.
              - name: "ELASTICSEARCH_HOSTS"
                value: "{es_endpoint}:{es_port}"
              # Specify the authentication information for the ACK cluster to access the Elasticsearch cluster.
              - name: "ELASTICSEARCH_USER"
                value: "{es_username}"
              - name: "ELASTICSEARCH_PASSWORD"
                value: "{es_password}"
            volumeMounts:
            - name: sock
              mountPath: /var/run/docker.sock
            - name: root
              mountPath: /host
              readOnly: true
            - name: varlib
              mountPath: /var/lib/filebeat
            - name: varlog
              mountPath: /var/log/filebeat
            - name: localtime
              mountPath: /etc/localtime
              readOnly: true
            livenessProbe:
              failureThreshold: 3
              exec:
                command:
                - /pilot/healthz
              initialDelaySeconds: 10
              periodSeconds: 10
              successThreshold: 1
              timeoutSeconds: 2
            securityContext:
              capabilities:
                add:
                - SYS_ADMIN
          terminationGracePeriodSeconds: 30
          volumes:
          - name: sock
            hostPath:
              path: /var/run/docker.sock
          - name: root
            hostPath:
              path: /
          - name: varlib
            hostPath:
              path: /var/lib/filebeat
              type: DirectoryOrCreate
          - name: varlog
            hostPath:
              path: /var/log/filebeat
              type: DirectoryOrCreate
          - name: localtime
            hostPath:
              path: /etc/localtime
    ```

    |Parameter|Description|
    |---------|-----------|
    |\{es\_endpoint\}|The endpoint of the Elasticsearch cluster.|
    |\{es\_port\}|    -   If the ACK cluster and the Elasticsearch cluster are deployed in the same virtual private cloud \(VPC\), set the endpoint to the **internal network address** of the Elasticsearch cluster. On the details page of the Elasticsearch cluster, you can obtain the internal network address in the Basic Information section.
    -   If the ACK cluster and the Elasticsearch cluster are not deployed in the same VPC, set the endpoint to the **public network address** of the Elasticsearch cluster. On the details page of the Elasticsearch cluster, you can obtain the public network address in the Basic Information section. |
    |\{es\_username\}|The username that is used to access the Elasticsearch cluster. Enter the username that is specified when you create the Elasticsearch cluster.|
    |\{es\_password\}|The password that is used to access the Elasticsearch cluster. Enter the password that is specified when you create the Elasticsearch cluster.|

8.  On the **Workload** page, click the **DaemonSets** tab.

    Select the used ACK cluster and select **kube-system** from the Namespace drop-down list. If the created DaemonSet is as shown in the following figure, this indicates that Log-pilot is running as expected.

    ![pilot](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2341258951/p133207.png)


**Note:** You can also use kubectl to connect to the ACK cluster. For more information, see [Connect to Kubernetes clusters through kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md). Then, you can run the following commands to use the preceding YAML temple to deploy a DaemonSet and query the status of the Log-pilot component:

```
~ kubectl apply -f log-pilot.yml
daemonset.extensions "log-pilot" created
~ kubectl -n kube-system get pod | grep log-pilot
log-pilot-458nj 1/1 Running 0 23s
log-pilot-8ld4n 1/1 Running 0 23s
log-pilot-b4kqv 1/1 Running 0 23s
log-pilot-gd588 1/1 Running 0 23s
log-pilot-k2ttk 1/1 Running 0 23s
```

## Step 3: Collect logs

In this example, a Deployment is created to collect logs of an Apache Tomcat container. This Deployment is used to test whether logs can be collected, indexed, and displayed as expected. You can also perform the same tests by using a StatefulSet.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the used ACK cluster and click the cluster name or **Details** in the **Actions** column.

    **Note:** Select the cluster where the Log-pilot component is deployed.

4.  In the left-side navigation pane, click **Workload**.

5.  Select the **namespace** where log-pilot is deployed and click the **Deployments** tab.

6.  In the upper-right corner of the Deployments tab, click **Create from Template** to go to the Create from Template page.

7.  Select **Custom** from the **Sample Template** drop-down list, paste the following content to the **template**, and then click **Create**.

    The following code block is a sample template:

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: tomcat
    spec:
      containers:
      - name: tomcat
        image: "tomcat:7.0"
        env:
        #1. value: "stdout" indicates that stdout files are collected.
        #2. name: aliyun_logs_catalina indicates that the stdout files are collected to the catalina index in the Elasticsearch cluster.
        - name: aliyun_logs_catalina
          value: "stdout"
        #1. value: "/usr/local/tomcat/logs/catalina.*.log" indicates that log files stored in the specified path are collected. Wildcard characters can be used in the value of the path.
        #2. name: aliyun_logs_access indicates that logs are collected to the access index in the Elasticsearch cluster.
        - name: aliyun_logs_access
          value: "/usr/local/tomcat/logs/catalina. *.log"
        # An emptyDir volume must be mounted to the path through which the log files are collected.
        volumeMounts.
          - name: tomcat-log
            mountPath: /usr/local/tomcat/logs
      volumes:
        - name: tomcat-log
          emptyDir: {}
    ```

    **Note:** The preceding template specifies the environment variables of an Apache Tomcat container in a pod to dynamically generate the configuration file for log collection. The following is the specified environment variables:

    -   `aliyun_logs_catalina=stdout` indicates that the stdout logs of the Tomcat container are collected.
    -   `aliyun_logs_access=/usr/local/tomcat/logs/catalina. *.log` indicates that all log files in the /usr/local/tomcat/logs/ directory whose names match catalina. \*.log are collected.
    In this scenario, the value of `$name` indicates the index that is used to store the logs. In this example, the values of `$name` indicate that the catalina and access indexes are used.

8.  On the **Workload** page, click the **Pods** tab. Select **default** from the Namespace drop-down list. Then, find and click the pod named **tomcat**.

    On the Pods - tomcat page, click the **Logs** tab. If the tab content is as shown in the following figure, this indicates that the pod is deployed.

    ![Deploy the Apache Tomcat container](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2341258951/p47226.png)

9.  Log on to the [Alibaba Cloud Elasticsearch console](https://elasticsearch.console.aliyun.com/?spm=5176.2020520152.products-grouped.delasticsearch.503816ddrx2e3K), and then navigate to the **Kibana console**. For more information, see log on to the Kibana console.

10. In the left-side navigation pane, click **Management**. On the Management page, click **Index Patterns**. Click **Create index...**, enter access-\* in the Create index pattern section, and then click **Next step**.

    ![Create an index](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4055359951/p47235.png)

11. Select **@timestamp** from the **Time Filter field name** drop-down list and click **Create index pattern**.

12. If you need to create multiple indexes, perform the following steps:

    1.  In the left-side navigation pane of the Kibana console, click **Management** and click **Index Patterns** in the Kibana section.

    2.  Click **Create index pattern**, enter the index \(the index format is XXX-\*\) in the Index pattern field, click **Next step**, and then click **Create**.


In the left-side navigation pane of the Kibana console, click **Discover**. You can find that the logs of the Apache Tomcat container are collected to the specified Elasticsearch cluster.

![Collect logs to the Elasticsearch cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4055359951/p47233.png)

**Note:** By default, when you use Log-pilot to collect logs to an Elasticsearch cluster, Log-pilot automatically creates indexes in the format of index-yyyy.MM.dd.

The preceding log collection configurations provide a quick and convenient way to collect stdout files and log files inside containers to an Elasticsearch cluster. Log-pilot can be used to efficiently collect logs from Kubernetes clusters. You can improve the efficiency of operations and maintenance \(O&M\) and ensure the stability of Kubernetes clusters by analyzing the collected logs.

