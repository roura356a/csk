# Collect logs of a Kubernetes cluster by using Log-Pilot, Elasticsearch, and Kibana {#concept_bmg_fkb_wdb .concept}

This topic describes how to collect logs of a Kubernetes cluster by using Log-Pilot, Elasticsearch, and Kibana.

Requirements for logs of distributed Kubernetes clusters always bother developers. This is mainly because of the characteristics of containers and the defects of log collection tools.

-   Characteristics of containers:
    -   Many collection targets: The characteristics of containers cause the number of collection targets is large, which requires to collect the container logs and container stdout. Currently, no good tool can collect file logs from containers dynamically. Different data sources have different collection softwares. However, no one-stop collection tool exists.
    -   Difficulty caused by auto scaling: Kubernetes clusters are in the distributed mode. The auto scaling of services and the environment brings great difficulty to log collection. You cannot configure the log collection path in advance, the same as what you do in the traditional virtual machine \(VM\) environment. The dynamic collection and data integrity are great challenges.
-   Defects of current log collection tools:
    -   Lack the capability to dynamically configure log collection: The current log collection tools require you to manually configure the log collection method and path in advance. These tools cannot dynamically configure the log collection because they cannot automatically detect the lifecycle changes or dynamic migration of containers.
    -   Log collection problems such as logs are duplicate or lost: Some of the current log collection tools collect logs by using the tail method. Logs may be lost in this way. For example, the application is writing logs when the log collection tool is being restarted. Logs written during this period may be lost. Generally, the conservative solution is to collect logs of 1 MB or 2 MB previous to the current log by default. However, this may cause the duplicate log collection.
    -   Log sources without clear marks: An application may have multiple containers that output the same application logs. After all the application logs are collected to a unified log storage backend, you cannot know a log is generated on which application container of which node when querying logs.

This document introduces Log-Pilot, a tool to collect Docker logs, and uses the tool together with Elasticsearch and Kibana to provide a one-stop solution to log collection problems in the Kubernetes environment.

## Introduction on Log-Pilot {#section_eys_gkb_wdb .section}

Log-Pilot is an intelligent tool used to collect container logs, which not only collects container logs and outputs these logs to multiple types of log storage backends efficiently and conveniently, but also dynamically discovers and collects log files from containers.

Log-Pilot uses declarative configuration to manage container events strongly and obtain the stdout and file logs of containers, which solves the problem of auto scaling. Besides, Log-Pilot has the functions of automatic discovery, maintenance of checkpoint and handle, and automatic tagging for log data, which effectively deals with the problems such as dynamic configuration, duplicate logs, lost logs, and log source marking.

[Log-Pilot](https://github.com/AliyunContainerService/log-pilot) is an open-source project in GitHub.

## Declarative configuration for container logs {#section_fys_gkb_wdb .section}

Log-Pilot supports managing container events, can dynamically listen to the event changes of containers, parse the changes according to the container labels, generate the configuration file of log collection, and then provide the file to collection plug-in to collect logs.

For Kubernetes clusters, Log-Pilot can dynamically generate the configuration file of log collection according to the environment variable `aliyun_logs_$name = $path`. This environment variable contains the following two variables:

-   One variable is $name, a custom string which indicates different meanings in different scenarios. In this scenario, $name indicates index when collecting logs to Elasticsearch.
-   The other is $path which supports two input modes, stdout and paths of log files within containers, respectively corresponding to the standard output of logs and log files within containers.
    -   Stdout indicates to collect standard output logs from containers. In this example, to collect Tomcat container logs, configure the label `aliyun.logs.catalina=stdout` to collect standard output logs of Tomcat.
    -   The path of a log file within a container also supports wildcards. To collect logs within the Tomcat container, configure the environment variable `aliyun_logs_access=/usr/local/tomcat/logs/*.log`. To not use the keyword aliyun, you can use the environment variable PILOT\_LOG\_PREFIX, which is also provided by Log-Pilot, to specify the prefix of your declarative log configuration. For example, `PILOT_LOG_PREFIX: "aliyun,custom"`.

Besides, Log-Pilot supports multiple log parsing formats, including none, JSON, CSV, Nginx, apache2, and regxp. You can use the `aliyun_logs_$name_format=<format>` label to tell Log-Pilot to use what format to parse logs when collecting logs.

Log-Pilot also supports custom tags. If you configure `aliyun_logs_$name_tags="K1=V1,K2=V2"` in the environment variable, K1=V1 and K2=V2 are collected to log output of the container during the log collection. Custom tags help you tag the log generation environment for convenient statistics, routing, and filter of logs.

## Log collection mode {#section_iys_gkb_wdb .section}

In this document, deploy a Log-Pilot on each machine and collect all the Docker application logs from the machines.

Compared with deploying a logging container on each pod, the most obvious advantage of this solution is less occupied resources. The larger the cluster scale is, the more obvious the advantage is. This solution is also recommended in the community.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6945/15646598544695_en-US.png)

## Prerequisites {#section_eeu_npy_ksj .section}

-   A Kubernetes cluster is created with ACK. For more information, see [Create a Kubernetes cluster](intl.en-US//Create a Kubernetes cluster.md#).
-   An Elasticsearch instance is created. For more information, see [Activate Alibaba Cloud Elasticsearch](../../../../intl.en-US/Quick Start/Activate Alibaba Cloud Elasticsearch.md#).
-   The Elasticsearch cluster can be accessed through the intranet or Internet.

## Procedure overview {#section_fvd_yb9_110 .section}

-   Step 1: Enable the Auto Indexing feature for the target Elasticsearch instance
-   Step 2: Deploy the Log-Pilot components on your Kubernetes cluster
-   Step 3: Collect logs of the target application

## Procedure {#section_3l4_50f_6ic .section}

## Step 1: Enable the Auto Indexing feature for the target Elasticsearch instance {#section_ge6_ycd_swm .section}

**Note:** Alibaba Cloud Elasticsearch by default disables the **Auto Indexing** feature. However, to automatically collect container logs, Log-Pilot automatically creates indexes according to the configurations of log collection. Therefore, you must enable the **Auto Indexing** feature.

1.  Log on to the Elasticsearch console.
2.  Find the target Elasticsearch instance, and then, in the **Actions** column, click **Manage**.
3.  In the left-side navigation pane, click **Cluster Configuration**. Then, on the right side of the page, click **Modify Configuration**.
4.  On the right of Auto Indexing, select the Enable radio button. Then, on the bottom of the page, select the check box **This operation will restart the instance. Continue?**, and click **OK**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16698/156465985545076_en-US.png)


## Step 2: Deploy the Log-Pilot components on your Kubernetes cluster {#section_apb_ag3_318 .section}

To save resources for cluster nodes, deploy the Log-Pilot components as a DaemonSet object on each node of the target Kubernetes cluster.

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Application** \> **Deployment**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/129974/156465985539478_en-US.png)

3.  In the upper-right corner, click **Create from Template**.
4.  Select the target cluster where you want to deploy the Log-Pilot component, and select the **kube-system** namespace.
5.  Select **Custom** from the **Sample Template** drop-down list, copy the follow code into the **Template** area, and click **Create**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16698/156465985545109_en-US.png)

    ``` {#codeblock_w3o_maf_dlu}
    apiVersion: extensions/v1beta1
    kind: DaemonSet
    metadata:
    name: log-pilot
    labels:
    app: log-pilot
    # The target namespace where you want to deploy log-pilot.
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
    # Deploy log-plilot on the Master nodes.
    tolerations:
    - key: node-role.kubernetes.io/master
    effect: NoSchedule
    containers:
    - name: log-pilot
    # For information about the log-pilot version, see https://github.com/AliyunContainerService/log-pilot/releases
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
    # Make sure that your Kubernetes cluster can access the target Elasticsearch instance.
    - name: "ELASTICSEARCH_HOSTS"
    value: "{es_endpoint}:{es_port}"
    # Set the permissions to access the target Elasticsearch instance.
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

    -   `{es_endpoint}`: indicates the address used to access the target Elasticsearch instance.

        **Note:** The address of the target Elasticsearch is shown on the Basic Information page of the Elasticsearch console.

        -   If your Kubernetes cluster and the target Elasticsearch instance are deployed within the same VPC, set this parameter as the **Internal Network Address** of the Elasticsearch instance.
        -   If your Kubernetes cluster and the target Elasticsearch instance are deployed in different VPCs, set this parameter as the **Public Network Address** of the Elasticsearch instance.
    -   `{es_port}`: indicates the port used to access the target Elasticsearch instance. We recommend that you set it as 9200.
    -   `{es_username}`: indicates the user name used to access the target Elasticsearch instance. The default is elastic.
    -   `{es_password}`: indicates the password used to access the target Elasticsearch instance. It is the password that you set when you created the Elasticsearch instance.
6.  Click **Kubernetes Dashboard** to view the progress.
7.  In the left-side navigation pane, select **kube-system** from the **Namespace** drop-down list. Then, choose **Workloads** \> **Pods** to verify that the Log-Pilot components are in the **Running** status.

    **Note:** You can also use [kubectl](intl.en-US//Connect to a Kubernetes cluster by using kubectl.md#) to access your Kubernetes cluster, and then run the following commands to view the status of the Log-Pilot components:

    ``` {#codeblock_0lt_cqc_4sb}
    ~ kubectl apply -f log-pilot.yml
    daemonset.extensions "log-pilot" created
    ~ kubectl -n kube-system get pod | grep log-pilot
    log-pilot-458nj 1/1 Running 0 23s
    log-pilot-8ld4n 1/1 Running 0 23s
    log-pilot-b4kqv 1/1 Running 0 23s
    log-pilot-gd588 1/1 Running 0 23s
    log-pilot-k2ttk 1/1 Running 0 23s
    ```


## Step 3: Collect logs of the target application {#section_69v_2bh_031 .section}

A Tomcat application is deployed in the Kubernetes cluster as the example to test whether logs of the application can be collected, indexed, and displayed.

**Note:** In this example, the application type of the Tomcat application is set as Deployment. The configurations are also applicable to the application type of StatefulSet.

1.  In the left-side navigation pane under Container Service-Kubernetes, choose **Application** \> **Deployment**.
2.  In the upper-right corner, click **Create from Template**.
3.  Select the same cluster as that you selected to deploy the Log-Pilot components, and select the target namespace.
4.  Select **Custom** from the **Sample Template** drop-down list, copy the follow code into the **Template** area, and click **Create**.

    ``` {#codeblock_71h_7h4_fdy}
    apiVersion: v1
    kind: Pod
    metadata:
    name: tomcat
    spec:
    containers:
    - name: tomcat
    image: "tomcat:7.0"
    env:
    # 1. stdout is a fixed keyword that indicates to collect standard output logs.
    # 2. Collect the standard output logs to the catalina index of the target Elasticsearch instance.
    - name: aliyun_logs_catalina
    value: "stdout"
    # 1. Collect container logs what are in file format. Wildcard characters are supported.
    # 2. Collect the logs to the access index of the target Elasticsearch instance.
    - name: aliyun_logs_access
    value: "/usr/local/tomcat/logs/catalina.*.log"
    # To set the path of the container logs that are in the file format, set the emptyDir parameter.
    volumeMounts:
    - name: tomcat-log
    mountPath: /usr/local/tomcat/logs
    volumes:
    - name: tomcat-log
    emptyDir: {}
    ```

    By customizing environment variables in a pod, the preceding orchestration file dynamically generates the configuration file to collect logs. The following settings of environment variables:

    -   `aliyun_logs_catalina=stdout` indicates to collect the stdout logs of the target containers.
    -   `aliyun_logs_access=/usr/local/tomcat/logs/catalina.*.log` indicates to collect the logs \(in the file format\) that meet the following requirements:
        -   The logs are generated in the directory of /usr/local/tomcat/logs/.
        -   The log names match the form of catalina.\*.log.
    In this example, the `$name` parameter in the environment variables indicates the catalina access indexes.

5.  In the left-side navigation pane under Container Service-Kubernetes, choose **Application** \> **Pods**.
6.  Select the target cluster and namespace where you deployed the Tomcat application. Then, in the **Action** column, choose **More** \> **Logs**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16698/156465985547225_en-US.png)

    The following logs displayed in the **Logs** tab of the Pods - tomcat page indicates that the Tomcat application is deployed in the target Kubernetes cluster.

7.  On the Elasticsearch console, click **Kibana console** to log on to Kibana.
8.  In the left-side navigation pane, click **Management**. Then, in the Configure an index pattern area, enter access-\*, and click **Create**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16698/156465987847235_en-US.png)

9.  **\(Optional\)**: Create multiple indexes.
    1.  Click **Management**. Then, in the Kibana area, click **Index Patterns**.
    2.  Click **Create Index Patterns**. Then, in the Index name or pattern area, enter target indexes in the format of XXX-\*, and click **Create**.
10. In the left-side navigation pane, click **Discover** to verify that the logs of the Tomcat application are collected to the target Elasticsearch instance.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16698/156465987847233_en-US.png)

    **Note:** By default, when Log-Pilot collects logs to an Elasticsearch instance, Log-Pilot creates indexes in the format of index-yyyy.MM.dd.


