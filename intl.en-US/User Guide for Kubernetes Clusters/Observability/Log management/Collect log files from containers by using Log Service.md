---
keyword: [log files from containers, log collection, Log Service]
---

# Collect log files from containers by using Log Service

Container Service for Kubernetes \(ACK\) is integrated with Log Service. When you create a cluster, you can enable Log Service to collect log files from containers, including standard outputs \(stdout\) and text files.

## Step 1: Install Logtail

When you create a cluster, select **Enable Log service** to install Logtail. You can also install Logtail for an existing cluster.

**Install Logtail when you create a cluster:**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  In the upper-right corner of the Clusters page, click **Create Kubernetes Cluster**.

    In this example, only the steps to enable Log Service are described. For more information about how to create an ACK cluster, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

4.  In the **Component Configurations** step, select **Enable Log Service** to install Logtail for the Kubernetes cluster to be created.

    When the **Enable Log Service** check box is selected, the system prompts you to create a Log Service project. For more information about how log files are managed in Log Service, see [Projects](/intl.en-US/Product Introduction/Basic concepts/Projects.md)\). Select one of the following methods to specify a project in one of the following ways:

    -   You can select an existing project to manage the collected log files.

        ![Select an existing project](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7002998161/p148932.png)

    -   You can click Create Project. Then, a project named `k8s-log-{ClusterID}` is automatically created to manage the collected log files. ClusterID indicates the unique ID of the cluster to be created.

        ![Create a project](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7002998161/p148933.png)

5.  After you set the parameters, click **Create Cluster** in the lower-right corner. In the message that appears, click **OK**.

    After the ACK cluster is created, you can find the cluster that has Logtail enabled on the Clusters page.


**Install Logtail for an existing cluster:**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Add-ons**. In the **Logs and Monitoring** section, find **logtail-ds**.

5.  Click **Install** next to **logtail-ds**.

6.  In the **Note** message, click **OK**.


If an earlier version of Logtail is installed, click **Upgrade** next to **logtail-ds**.

## Step 2: Configure Log Service when you create an application

You can configure Log Service to collect log files from containers when you create an application. You can use the wizard in the console or YAML templates to create applications.

**Use the wizard in the console to create an application and configure Log Service**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  On the Deployments page, select a namespace from the **Namespace** drop-down list. Then, click **Create from Image** in the upper-right corner of the page.

6.  In the **Basic Information** step, specify **Name**, **Replicas**, and **Type**. Then, click **Next** to go to the Container step.

    The following steps describe how to configure Log Service. For more information about other configuration items, see [Use a Deployment to create a stateless application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Use a Deployment to create a stateless application.md).

7.  In the **Log** section, click the plus sign \(**+**\) to add a Logstore. You must specify **Logstore** and **Log Path in Container \(Can be set to stdout\)**.

    -   Logstore: specifies the Logstore that stores collected log files. If the specified Logstore does not exist, the system automatically creates a Logstore in the Log Service project that is associated with the cluster.

        **Note:** The name of the Logstore cannot contain underscores \(\_\). You can use hyphens \(-\) instead.

    -   Log Path in Container \(Can be set to stdout\): specifies the path from which you want to collect log files. For example, /usr/local/tomcat/logs/catalina.\*.log indicates that log files from the Tomcat application are collected.

        **Note:** If you set the value to stdout, standard outputs and error messages are collected.

        After you add a configuration entry, the system automatically creates a Logstore. By default, logtail-ds collects log files in simple mode. In this mode, log files are collected by line. To use more log collection methods, go to the [Log Service console](https://sls.console.aliyun.com/lognext/profile) and modify the log collection configurations of the project and Logstore. By default, the project uses k8s-log as the prefix.

    ![Log collection configurations](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9826572061/p148949.png)

8.  Add **Custom Tag**.

    Click the plus sign \(**+**\) to add custom tags. Each tag is a key-value pair that is appended to the collected log files. You can use custom tags to mark log data. For example, you can use a tag to denote the application version.

    ![Custom tags](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1055359951/p9473.png)

9.  After you set the other parameters, click **Next** to set advanced settings. For more information about advanced settings, see [Use a Deployment to create a stateless application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Use a Deployment to create a stateless application.md).


**Create an application by using a YAML template**

1.  In the left-side navigation pane of the cluster details page, click **Workloads**.

2.  On the Deployments page, select a namespace from the **Namespace** drop-down list. Then, click **Create from YAML** in the upper-right corner of the page.

3.  The syntax of the YAML template is the same as that of Kubernetes. You can use `env` to define **collection configurations** and **custom tags**. You must also configure volumeMounts and volumes. The following is an example of a pod for collecting log files:

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: my-demo
    spec:
      containers:
      - name: my-demo-app
        image: 'registry.cn-hangzhou.aliyuncs.com/log-service/docker-log-test:latest'
        env:
        ######### Configure environment variables ###########
        - name: aliyun_logs_log-stdout
          value: stdout
        - name: aliyun_logs_log-varlog
          value: /var/log/*.log
        - name: aliyun_logs_mytag1_tags
          value: tag1=v1
        ###############################
        ######### Configure volume mounting ###########
        volumeMounts:
        - name: volumn-sls-mydemo
          mountPath: /var/log
      volumes:
      - name: volumn-sls-mydemo
        emptyDir: {}
      ###############################
    ```

    Perform the following steps in sequence based on your needs:

    **Note:** If you have more requirements for log collection, see [Step 3: Configure advanced parameters in the env field](#section_q8z_yc4_dvn).

    1.  Add **collection configurations** and **custom tags** by using environment variables. All environment variables related to log collection must use `aliyun_logs_` as the prefix.
        -   Add log collection configurations in the following format:

            ```
            - name: aliyun_logs_{Logstore name}
              value: {Log file path}                            
            ```

            In the preceding YAML template, two environment variables are added to the log collection configuration. Environment variable `aliyun_logs_log-stdout` indicates that a Logstore named log-stdout is created to store the stdout files collected from containers.

            **Note:** The name of the Logstore cannot contain underscores \(\_\). You can use hyphens \(-\) instead.

        -   **Custom tags** must be specified in the following format:

            ```
            - name: aliyun_logs_{Tag name without underscores (_)}_tags
              value: {Tag name }={Tag value}                            
            ```

            After a tag is added, the tag is automatically appended to the collected log files when logtail-ds collects the container logs.

    2.  If you specify a log path to collect log files that are not standard outputs, you must configure volumeMounts.

        In the preceding YAML template, the mountPath field in volumeMounts is set to /var/log. This allows logtail-ds to collect log files from the /var/log/\*.log directory.

4.  After you modify the YAML template, click **Create** to submit the configurations.


## Step 3: Configure advanced parameters in the env field

You can specify multiple parameters in the env field to configure log collection. The following table describes the parameters.

**Note:** This configuration method is not applicable to edge computing scenarios.

|Field|Description|Example|Precaution|
|-----|-----------|-------|----------|
|aliyun\_logs\_\{key\}|-   Required.\{key\} can contain only lowercase letters, digits, and hyphens \(-\).
-   If the specified aliyun\_logs\_\{key\}\_logstore does not exist, a Logstore named \{key\} is created.
-   To collect standard outputs of container log files, set the value to stdout. You can also set the value to a path inside a container.

|-   ```
- name: aliyun_logs_catalina

   stdout
```

-   ```
- name: aliyun_logs_access-log

   /var/log/nginx/access.log
```


|-   By default, logtail-ds collects log files in simple mode. In this case, the collected log files are not parsed. If you want to parse the log files, we recommend that you change the collection mode in the Log Service console. For more information, see [Use the console to collect Kubernetes text files in DaemonSet mode](/intl.en-US/Data Collection/Logtail collection/Container log collection/Use the console to collect Kubernetes text logs in DaemonSet mode.md), [Use the console to collect Kubernetes stdout and stderr outputs in DaemonSet mode](/intl.en-US/Data Collection/Logtail collection/Container log collection/Use the console to collect Kubernetes stdout and stderr logs in DaemonSet mode.md), or [Use CRDs to collect Kubernetes container logs in DaemonSet mode](/intl.en-US/Data Collection/Logtail collection/Container log collection/Use CRDs to collect Kubernetes container logs in DaemonSet mode.md).
-   The value of \{key\} must be unique in the cluster. |
|aliyun\_logs\_\{key\}\_tags|Optional. This parameter is used to add tags to log data. The value must be in the following format: \{tag-key\}=\{tag-value\}.|```
- name: aliyun_logs_catalina_tags

   app=catalina
```

|-|
|aliyun\_logs\_\{key\}\_project|Optional. This parameter specifies a project in Log Service. By default, the project that you specified when you create the cluster is used.|```
- name: aliyun_logs_catalina_project

   my-k8s-project
```

|The region of the project must be the same as where your logtail-ds is deployed.|
|aliyun\_logs\_\{key\}\_logstore|Optional. This variable specifies a Logstore in Log Service. By default, the Logstore is named after \{key\}.|```
- name: aliyun_logs_catalina_logstore

   my-logstore
```

|-|
|aliyun\_logs\_\{key\}\_shard|Optional. This variable specifies the number of shards in the Logstore. Valid values: 1 to 10. Default value: 2.|```
- name: aliyun_logs_catalina_shard

   4
```

|-|
|aliyun\_logs\_\{key\}\_ttl|Optional. This parameter specifies the number of days for which log data is retained. Valid values: 1 to 3650. -   To permanently retain log data, set the value to 3650.
-   Default value: 90.

|```
- name: aliyun_logs_catalina_ttl

   3650
```

|-|
|aliyun\_logs\_\{key\}\_machinegroup|Optional. This parameter specifies the machine group of the application. By default, the machine group is the one where your logtail-ds is deployed.|```
- name: aliyun_logs_catalina_machinegroup

   my-machine-group
```

|-|

-   **Scenario 1: Collect log files from multiple applications and store them in the same Logstore**

    In this scenario, set the aliyun\_logs\_\{key\}\_logstore parameter. The following example shows how to collect standard outputs from two applications and store the outputs in stdout-logstore.

    Configure the following environment variables for Application 1:

    ```
    ######### Configure environment variables ###########
        - name: aliyun_logs_app1-stdout
          value: stdout
        - name: aliyun_logs_app1-stdout_logstore
          value: stdout-logstore
    ```

    Configure the following environment variables for Application 2:

    ```
    ######### Configure environment variables ###########
        - name: aliyun_logs_app2-stdout
          value: stdout
        - name: aliyun_logs_app2-stdout_logstore
          value: stdout-logstore
    ```

-   **Scenario 2: Collect log files from different applications and store them in different projects**

    In this scenario, perform the following steps:

    1.  Create a machine group in each project and set the machine group ID in the following format: k8s-group-\{cluster-id\}, where \{cluster-id\} is the ID of the cluster. You can customize the machine group name.
    2.  Specify the project, Logstore, and machine group in the environment variables for each application.

        ```
        ######### Configure environment variables ###########
            - name: aliyun_logs_app1-stdout
              value: stdout
            - name: aliyun_logs_app1-stdout_project
              value: app1-project
            - name: aliyun_logs_app1-stdout_logstore
              value: app1-logstore
            - name: aliyun_logs_app1-stdout_machinegroup
              value: app1-machine-group
        ```


## Step 4: View log files

The following example shows how to view log files of the Tomcat application created by using the wizard in the console. Log files from the Tomcat application are stored in Log Service. You can log on to the Log Service console to view log files collected from the containers. Perform the following steps to view log files:

1.  Log on to the [Log Service console](https://sls.console.aliyun.com).

2.  In the **Projects** section, click the project that is associated with the Kubernetes cluster to go to the **Logstores** tab. By default, the project name is in the format of k8s-log-\{Kubernetes cluster ID\}.

3.  In the Logstores list, find the Logstore that is specified when you configure log collection. Move the pointer over the Logstore name and click the ![button](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9826572061/p88516.png) icon. Then, click **Search & Analysis**.

4.  In this example, you can view standard outputs of the Tomcat application and text log files of containers. You can also find that custom tags are appended to the collected log files.

    ![Log fields](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2055359951/p9541.png)


After Log Service is enabled for the application, you can view log files of containers in the ACK console. Perform the following steps to view log files of containers:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Event Center**.

5.  On the **Log Center** page, click the **Application Logs** tab and specify the filter conditions. Then, click **Select Logstore** to view log files of containers.


## More information

1.  By default, Log Service collects log files by line and does not parse the log files. If you want to change the log collection mode and parse the log files, modify the log collection configurations in the Log Service console. For more information, see the following topics:
    -   [Use the console to collect Kubernetes text log files in DaemonSet mode](/intl.en-US/Data Collection/Logtail collection/Container log collection/Use the console to collect Kubernetes text logs in DaemonSet mode.md)
    -   [Use the console to collect Kubernetes stdout and stderr outputs in DaemonSet mode](/intl.en-US/Data Collection/Logtail collection/Container log collection/Use the console to collect Kubernetes stdout and stderr logs in DaemonSet mode.md)
2.  You can also use CustomResourceDefinitions \(CRDs\) to collect log files from Kubernetes clusters
3.  For more information about troubleshooting, see [Troubleshoot collection errors]().

