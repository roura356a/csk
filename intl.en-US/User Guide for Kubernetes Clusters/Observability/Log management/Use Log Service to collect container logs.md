---
keyword: [container logs, log collection, Log Service]
---

# Use Log Service to collect container logs

Container Service for Kubernetes \(ACK\) is integrated with Log Service. When you create a cluster, you can enable Log Service to collect container logs, including standard outputs and text files.

## Step 1: Activate Logtail

When you create a cluster, select **Enable Log service** to activate Logtail. You can also install Logtail for an existing cluster.

**Activate Logtail when you create a cluster:**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, click **Create Kubernetes Cluster** in the upper-right corner. For more information, see [Create a cluster]().

4.  In the **Select Cluster Template** dialog box, select a cluster type.

5.  In the **Component Configurations** step, select the **Log Service** check box to install Logtail for the newly created Kubernetes cluster.

6.  After you select the **Enable Log Service** check box, you must specify a project to manage log data. For more information about projects, see [Projects](/intl.en-US/Product Introduction/Basic concepts/Projects.md).

    -   Select an existing project.

        ![Select an existing project](../images/p148932.png)

    -   Create a project. By default, the system names the project in the format of `k8s-log-{ClusterID}`. ClusterID indicates the unique ID of the cluster to be created.

        ![Create a project](../images/p148933.png)

7.  After you set the parameters, click **Create Cluster** in the lower-right corner. In the message that appears, click **OK**.

    On the Clusters page, you can find the created cluster.


**Install Logtail for an existing cluster:**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the cluster details page, choose **Operations** \> **Add-ons**. In the **Optional Add-ons** section, find **logtail-ds**.

5.  Click **Install** next to **logtail-ds**.

    ![Install logtail-ds](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1996549951/p83541.png)

6.  In the **Confirm** message, click **OK**.


If you have installed an earlier version of Logtail, you can upgrade the Logtail in the **Optional Add-ons** section.

![Upgrade logtail-ds](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0591103951/p83545.png)

## Step 2: Configure Log Service when you create an application

You can configure Log Service to collect container logs when you create an application. You can use the wizard in the console or YAML templates to create applications.

**Use the wizard to create an application:**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  Click the **Deployment** tab and select a **Namespace** from the drop-down list where you want to create an application, and then click **Create from Image** in the upper-right corner.

6.  In the **Basic Information** step, specify **Name**, **Replicas**, and **Type**. Then, click **Next** to go to the Container step.

    ![Basic information](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8826572061/p148945.png)

7.  In the Container step, select an image. In this example, select an NGINX image.

    The following steps describe how to configure Log Service. For more information about other configuration items, see [Deploy a stateless application from an image](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Deploy a stateless application from an image.md).

8.  In the **Log** section, click the plus sign \(**+**\) to add a Logstore. You must specify **Logstore** and **Log Path in Container \(Can be set to stdout\)**.

    -   Logstore: specifies the Logstore that stores collected logs. If the specified Logstore does not exist, the system automatically creates a Logstore in the Log Service project that is associated with the cluster.

        **Note:** The name of the Logstore can contain hyphens \(-\) but cannot contain underscores \(\_\).

    -   Log Path in Container \(Can be set to stdout\): specifies the path from which you want to collect logs. For example, /usr/local/tomcat/logs/catalina. \*.log indicates Tomcat log files.

        **Note:** If you set the value to stdout, standard outputs and error messages are collected.

        After you add a collection configuration, the system automatically creates a Logstore. By default, logtail-ds collects logs in simple mode. This mode collects logs by line. To use more collection methods, go to the [Log Service console](https://sls.console.aliyun.com/lognext/profile) and modify the log collection configurations of the project and Logstore. By default, the project uses k8s-log as the prefix.

    ![Collection configuration](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9826572061/p148949.png)

9.  **Add custom tags**.

    Click the plus sign \(**+**\) to add custom tags. Each tag is a key-value pair that is appended to collected logs. You can use custom tags to mark log data. For example, you can use a tag to denote the application version.

    ![Custom tags](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1055359951/p9473.png)

10. After you set all parameters, click **Next** to configure advanced settings. For more information about advanced settings, see [Deploy a stateless application from an image](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Deploy a stateless application from an image.md).


**Use a YAML template to create an application**

1.  In the left-side navigation pane of the cluster details page, click **Workload**.

2.  Click the **Deployment** tab and select a **Namespace** from the drop-down list where you want to create an application, and then click **Create from Template** in the upper-right corner.

3.  YAML templates follow the Kubernetes syntax. You can use environment variables to define **collection configurations** and **custom tags**. You must also configure volumeMounts and volumes. The following sample template shows how to create a pod:

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

    Perform the following steps based on your needs:

    **Note:** If you have more log collection requirements, see [\(Optional\) Step 3: Configure advanced parameters in the env field](#section_q8z_yc4_dvn).

    1.  Use environment variables to add **collection configurations** and **custom tags**. All environment variables related to log collection must use `aliyun_logs_` as the prefix.
        -   Add collection configurations in the following format:

            ```
            - name: aliyun_logs_{Logstore name}
              value: {Log path}                            
            ```

            In the preceding YAML template, two environment variables are added to the collection configuration. Environment variable `aliyun_logs_log-stdout` indicates that a Logstore named log-stdout is created to store the stdout files from containers.

            **Note:** The name of a Logstore can contain hyphens \(-\) but cannot contain underscores \(\_\).

        -   **Custom tags** must be specified in the following format:

            ```
            - name: aliyun_logs_{Tag name without underscores (_)}_tags
              value: {Tag name}={Tag value}                            
            ```

            After a tag is added, the tag is automatically appended to log fields when logtail-ds collects the container logs.

    2.  If you specify a log path to collect logs that are not standard outputs, you must configure volumeMounts.

        In the preceding YAML template, the mountPath field in volumeMounts is set to /var/log. This allows logtail-ds to collect /var/log/\*.log files.

4.  After you edit the YAML template, click **Create** to submit the configurations.


## \(Optional\) Step 3: Configure advanced parameters in the env field

You can specify multiple parameters in the env field to configure log collection. The following table lists the parameters.

**Note:** This configuration method is not applicable to edge computing scenarios.

|Field|Description|Reference|Precautions|
|-----|-----------|---------|-----------|
|aliyun\_logs\_\{key\}|-   Required. \{key\} can only contain lowercase letters, digits, and hyphens \(-\).
-   If the specified aliyun\_logs\_\{key\}\_logstore does not exist, a Logstore named \{key\} is created.
-   To collect standard outputs of container logs, set the value to stdout. You can also set the value to a path inside a container.

|-   ```
- name: aliyun_logs_catalina

   stdout
```

-   ```
- name: aliyun_logs_access-log

   /var/log/nginx/access.log
```


|-   By default, logtail-ds collects logs in simple mode. In this case, the collected logs are not parsed. If you want to parse the logs, we recommend that you modify the collection mode in the Log Service console. For more information, see [Use the console to collect Kubernetes text logs in DaemonSet mode](/intl.en-US/Data Collection/Logtail collection/Container log collection/Use the console to collect Kubernetes text logs in DaemonSet mode.md), [Use the console to collect Kubernetes stdout and stderr logs in DaemonSet mode](/intl.en-US/Data Collection/Logtail collection/Container log collection/Use the console to collect Kubernetes stdout and stderr logs in DaemonSet mode.md), or [Use CRDs to collect Kubernetes container logs in the DaemonSet mode](/intl.en-US/Data Collection/Logtail collection/Container log collection/Use CRDs to collect Kubernetes container logs in the DaemonSet mode.md).
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

|The region of the project must be the same as where your logtail-ds is located.|
|aliyun\_logs\_\{key\}\_logstore|Optional. This parameter specifies a Logstore in Log Service. By default, the Logstore is named after \{key\}.|```
- name: aliyun_logs_catalina_tags

   my-logstore
```

|-|
|aliyun\_logs\_\{key\}\_shard|Optional. This parameter specifies the number of shards in the Logstore. Valid values: 1 to 10. Default value: 2.|```
- name: aliyun_logs_catalina_shard

   4
```

|-|
|aliyun\_logs\_\{key\}\_ttl|Optional. This parameter specifies the number of days for which log data is retained. Valid values: 1 to 3650. -   To retain log data permanently, set the value to 3650.
-   Default value: 90.

|```
- name: aliyun_logs_catalina_ttl

   3650
```

|-|
|aliyun\_logs\_\{key\}\_machinegroup|Optional. This parameter specifies the machine group of the application. By default, the machine group is the one where your logtail-ds is located.|```
- name: aliyun_logs_catalina_machinegroup

   my-machine-group
```

|-|

-   **Scenario 1: Collect logs from multiple applications and store the logs in the same Logstore**

    In this scenario, set the aliyun\_logs\_\{key\}\_logstore parameter. The following example shows how to collect standard output logs from two applications and store the logs in stdout-logstore.

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

-   **Scenario 2: Collect logs from different applications and store the logs in different projects**

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


## Step 4: View logs

The following example shows how to view logs of the Tomcat application created in the console. Tomcat application logs are stored in Log Service. Perform the following steps to view logs:

1.  Log on to the [Log Service console](https://sls.console.aliyun.com).

2.  In the **Projects** section, click the project that is associated with the Kubernetes cluster to go to the **Logstores** tab. By default, the project name follows the format of k8s-log-\{Kubernetes cluster ID\}.

3.  In the Logstores list, find the Logstore that is specified when you configure log collection. Move the pointer over the Logstore name and click the ![button](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9826572061/p88516.png) icon. Then, click **Search & Analysis**.

4.  In this example, you can view standard outputs of the Tomcat application and container text files. You can also find that custom tags are appended to log fields.

    ![Log fields](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2055359951/p9541.png)


## More information

1.  By default, Log Service collects logs by line and does not parse the logs. If you want to change the log collection mode and parse the logs, modify the log collection configurations in the Log Service console. For more information, see the following topics:
    -   [Use the console to collect Kubernetes text logs in DaemonSet mode](/intl.en-US/Data Collection/Logtail collection/Container log collection/Use the console to collect Kubernetes text logs in DaemonSet mode.md)
    -   [Use the console to collect Kubernetes stdout and stderr logs in DaemonSet mode](/intl.en-US/Data Collection/Logtail collection/Container log collection/Use the console to collect Kubernetes stdout and stderr logs in DaemonSet mode.md)
2.  You can also use CRDs to collect Kubernetes cluster logs..
3.  For more information about troubleshooting, see [Troubleshoot collection errors]().

