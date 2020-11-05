---
keyword: [log-pilot, log collection, Elasticsearch, Kubernetes]
---

# Use log-pilot to collect logs of a cluster

The log-pilot component is an open source Docker log tool. This topic describes how to use the log-pilot component with Elasticsearch and Kibana to provide an all-in-one solution for collecting logs from Kubernetes clusters.

-   Container Service for Kubernetes \(ACK\) is activated and a cluster is created. For more information, see [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md).

    In this example, a cluster is created in the China \(Hangzhou\) region.

-   The ACK cluster is connected to an Elasticsearch cluster.

When you collect logs of distributed Kubernetes clusters, you may face the following challenges:

-   Challenges caused by the features of containers
    -   You may need to collect a large number of log files, such as logs inside containers and logs to standard output \(stdout\). However, existing log tools fail to dynamically collect logs inside containers in an efficient manner. Each type of logs can be collected by using a specific log tool. However, no all-in-one solution is available for collecting all types of logs.
    -   Kubernetes clusters are scalable and require a dynamic way to collect logs. A Kubernetes cluster is built based on a distributed architecture. The service and runtime of a Kubernetes cluster can be scaled in or out based on actual requirements. Therefore, log collection in Kubernetes clusters is more complicated than that in virtual machines where you can collect logs from specified paths. A dynamic and all-in-one log collection solution is required for Kubernetes clusters.
-   Defects of existing log tools
    -   Existing log tools do not support dynamic configuration for collecting logs. These log tools collect logs based on the predefined settings and paths. When pods move through their lifecycles or when containers are scaled, these tools cannot dynamically update the log collection settings or paths. As a result, log collection may fail.
    -   Some log tools may cause duplicate log collections or data loss. Some log tools use the --tail option of the Docker log collection command to collect logs. This may cause data loss. For example, a log tool does not collect log data when it restarts. As a result, the log data generated within this period of time is lost. The conventional way to resolve this issue is to collect log data that is generated one or two minutes earlier than the current time. As a result, the log tool may collect logs that have been collected before.
    -   The sources of the collected logs are unknown. An application may run on multiple pods that generate the same application logs. After all the application logs are collected and stored in your backend server, you may find that the logs have the same content. As a result, you cannot identify which pod on a node is the source of these logs.

## Introduction to log-pilot

The log-pilot component is an intelligent container log collection tool. You can use log-pilot to collect and export logs to multiple types of backend servers in an efficient manner. This component can also dynamically discover and collect logs inside containers.

The log-pilot component uses declarative configuration to define container events for collecting logs to stdout and logs inside containers. This allows you to collect logs when containers are scaled. The log-pilot component also supports automatic discovery, checkpoints, file handle holding, and automatic tagging for log data. These features can deal with log collection issues in an efficient manner, such as dynamic collection settings, duplicate log collections, data loss, and log source tagging.

The log-pilot component is an open source project on GitHub. For more information, visit [github.com/AliyunContainerService/log-pilot](https://github.com/AliyunContainerService/log-pilot).

## Declarative configuration for container log collection

The log-pilot component supports container event management. This component dynamically listens to changes of containers, parses the changes based on the environment variables of containers, and generates a configuration file for log collection. Then, log-pilot uses the configuration file to collect logs.

In a Kubernetes cluster, log-pilot dynamically generates a configuration file for log collection based on the `aliyun_logs_$name = $path` environment variable of a container. The environment variable includes two parameters:

-   $name: You can set the value to a custom string to meet the requirements in different scenarios. In this scenario, logs are collected to an Elasticsearch cluster. Therefore, the value of $name specifies the index where the log data resides in the Elasticsearch cluster.
-   $path: You can set the value to stdout or a container path. A value of stdout specifies the logs to stdout. A value of a path specifies the logs in a container path.
    -   If you set the value to stdout, logs are collected to stdout. In this example, set the environment variable to `aliyun.logs.catalina=stdout` to collect logs to stdout from an Apache Tomcat container.
    -   If you set the value to a path, log files in the specified path are collected. Wildcard characters can be used in the value of a path. In this example, set the environment variable to `aliyun_logs_access=/usr/local/tomcat/logs/*.log` to collect logs files inside the Apache Tomcat container. If you want to use a prefix other than aliyun, log-pilot provides the PILOT\_LOG\_PREFIX environment variable for you to customize the prefix of the declarative configuration, such as `PILOT_LOG_PREFIX: "aliyun,custom"`.

The log-pilot component can also parse logs in a variety of formats. You can use the `aliyun_logs_$name_format=<format>` environment variable to specify the formats of the collected logs. Valid values of the format parameter: none, json, csv, nginx, apache2, and regxp.

The log-pilot component allows you to add custom tags to the collected logs. For example, you can specify `aliyun_logs_$name_tags="K1=V1,K2=V2"`. in the environment variable. In this case, the collected logs have tags K1=V1 and K2=V2. You can add custom tags to an environment variable. In this case, you can query, route, and filter logs based on tags.

## Log collection mode

The following example describes how to deploy log-pilot to all nodes in a cluster to collect logs of containers that are running on each node.

Compared with the mode where a logging container is deployed on each pod, this mode consumes fewer resources. The log-pilot component offers distinct advantages if your cluster has a large number of nodes. This mode is also recommended by the Kubernetes community.

![Log collection mode](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2341258951/p10636.png)

## Step 1: Create an Elasticsearch cluster

**Note:** The Elasticsearch cluster version must be V6.x.x. For more information about how to create an Elasticsearch cluster, see [Create an Alibaba Cloud Elasticsearch cluster](/intl.en-US/Quick Start/Step 1: Create a cluster/Create an Alibaba Cloud Elasticsearch cluster.md). By default, an Elasticsearch cluster does not automatically create indexes for documents that are not indexed. However, when log-pilot collects container logs, it must create indexes for documents based on the log collection configuration. Therefore, you must enable the **Auto Indexing** feature.

Perform the following steps to enable the **Auto Indexing** feature:

1.  Log on to the [Elasticsearch console](https://elasticsearch.console.aliyun.com/#/instances).

2.  In the left-side navigation pane, click Elasticsearch Clusters. On the Clusters page, click the name of the cluster that you want to manage.

3.  In the left-side navigation pane, click **Cluster Configuration**. In the YML File Configuration section, click **Modify Configuration** on the right side of the page. The YML File Configuration pane appears on the right side of the page.

4.  Set Auto Indexing to **Enable**. Select the **This operation will restart the cluster. Continue?** check box, and click **OK**.


## Step 2: Deploy the log-pilot component

To reduce the consumption on node resources, log-pilot is deployed as a DaemonSet on each node of the cluster.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Workload**.

5.  Select **kube-system** from the **Namespace** drop-down list.

6.  On the **Deployments** tab, click **Create from Template** in the upper-right corner.

7.  Select **Custom** from the **Sample Template** drop-down list, copy the following code block to the **template**, and then click **Create**.

    ```
    apiVersion: extensions/v1beta1
    kind: DaemonSet
    metadata:
      name: log-pilot
      labels:
        app: log-pilot
      # Specify the namespace where you want to deploy log-pilot.
      namespace: kube-system
    spec:
      updateStrategy:
        type: RollingUpdate
      template:
        metadata:
          labels:
            app: log-pilot
          annotations:
            scheduler.alpha.kubernetes.io/critical-pod: ''
        spec:
          # Specify a toleration that allows log-pilot to be deployed to master nodes.
          - key: node-role.kubernetes.io/master
            effect: NoSchedule
          containers:
          - name: log-pilot
            # For more information about log-pilot versions, visit https://github.com/AliyunContainerService/log-pilot/releases.
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
              # Make sure that the ACK cluster can connect to the Elasticsearch cluster.
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
    |\{es\_port\}|    -   If the ACK cluster and the Elasticsearch cluster are deployed in the same virtual private cloud \(VPC\), set the value to the **internal endpoint** of the Elasticsearch cluster. On the details page of the Elasticsearch cluster, obtain the internal endpoint in the Basic Information section.
    -   If the ACK cluster and the Elasticsearch cluster are deployed in different VPCs, set the value to the **public endpoint** of the Elasticsearch cluster. On the details page of the Elasticsearch cluster, obtain the public endpoint in the Basic Information section. |
    |\{es\_username\}| |
    |\{es\_password\}|The password for connecting to the Elasticsearch cluster. Enter the password that you have specified during Elasticsearch cluster creation.|

8.  On the details page of the cluster, click **Workload** in the left-side navigation pane. On the page that appears, click the **DaemonSets** tab.

    Select the cluster that you want to manage and select **kube-system** from the Namespace drop-down list. If log-pilot appears, this component is deployed and running properly, as shown in the following figure.

    ![pilot](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2341258951/p133207.png)


**Note:** You can also use kubectl to connect to the ACK cluster. For more information, see [Connect to Kubernetes clusters through kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md). You can run the following commands to create a DaemonSet based on the preceding YAML template and query the running status of log-pilot.

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

The following example describes how to create a deployment to collect logs of an Apache Tomcat container. This deployment is used to test whether logs can be collected, indexed, and displayed as expected. You can also perform the same tests by using a StatefulSet.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the cluster name or click **Details** in the **Actions** column.

    **Note:** Select the cluster where you deploy log-pilot in Step 2.

4.  In the left-side navigation pane, click **Workload**.

5.  Select the **namespace** where log-pilot is deployed and click the **Deployments** tab.

6.  In the upper-right corner of the page, click **Create from Template**.

7.  Select **Custom** from the **Sample Template** drop-down list, copy the following code block to the **template**, and then click **Create**.

    The following content is a sample template:

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
        #1. Collect logs to stdout.
        #2. Collect logs to the catalina index of the Elasticsearch cluster.
        - name: aliyun_logs_catalina
          value: "stdout"
        #1. Collect logs from a specific path of the container. You can use a wildcard character (*) to collect logs whose names contain specific keywords.
        #2. Collect logs to the access index of the Elasticsearch cluster.
        - name: aliyun_logs_access
          value: "/usr/local/tomcat/logs/catalina. *.log"
        # You must mount an emptyDir volume to the path through which the logs are collected.
        volumeMounts
          - name: tomcat-log
            mountPath: /usr/local/tomcat/logs
      volumes:
        - name: tomcat-log
          emptyDir: {}
    ```

    **Note:** The preceding template defines the environment variables for a container. This enables log-pilot to dynamically generate configuration files for log collection. The following list describes the environment variables:

    -   `aliyun_logs_catalina=stdout` indicates that the container logs are collected to stdout.
    -   `aliyun_logs_access=/usr/local/tomcat/logs/catalina. *.log` indicates that all log files in the /usr/local/tomcat/logs/ path whose names contain catalina. \*.log are collected.
    In this scenario, `$name` in the environment variable indicates the index where the log files reside. In this example, the log files are located in the catalina and access indexes.``

8.  On the **Workload** page, click the **Pods** tab. Select **default** from the Namespace drop-down list and click the name of the container. In this example, click **tomcat**.

    On the Pods - tomcat page, click the **Logs** tab. If the following content appears, the Tomcat container is deployed.

    ![Deploy an Apache Tomcat container](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2341258951/p47226.png)

9.  Log on to the [Elasticsearch console](https://elasticsearch.console.aliyun.com/?spm=5176.2020520152.products-grouped.delasticsearch.503816ddrx2e3K), and then navigate to the **Kibana console**. For more information, see log on to the Kibana console.

10. In the left-side navigation pane, click **Management**. In the Kibana section, click **Index Patterns**. Then, click **Create index pattern**, enter access-\* in the Create index pattern section, and then click **Next step**.

    ![Create an index](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4055359951/p47235.png)

11. Select **@timestamp** from the **Time Filter field name** drop-down list, and click **Create index pattern**.

12. To create multiple indexes, perform the following steps:

    1.  In the left-side navigation pane of the Kibana console, click **Management**, and click **Index Patterns** in the Kibana section.

    2.  Click **Create index pattern**, enter the index in the Index pattern field, and then click **Next step**. Click **Create**. The index follows the xxx-\* format.


In the left-side navigation pane of the Kibana console, click **Discover**. You can find that the logs of the Tomcat container are collected to the specified Elasticsearch cluster.

![Collect logs to the Elasticsearch cluster](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4055359951/p47233.png)

**Note:** By default, when you use log-pilot to collect logs to an Elasticsearch cluster, log-pilot automatically creates indexes in the format of index-yyyy.mm.dd.

The preceding log collection configuration allows you to collect logs to stdout and logs inside containers to an Elasticsearch cluster. You can use log-pilot to collect and analyze logs of Kubernetes clusters in an efficient manner. This improves the efficiency of O&M and ensures the stability of Kubernetes clusters.

