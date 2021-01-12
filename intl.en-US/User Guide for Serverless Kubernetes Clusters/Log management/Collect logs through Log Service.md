# Collect logs through Log Service

This topic describes how to use Log Service to collect stdout files and log files from containers in serverless Kubernetes \(ASK\) clusters.

An ASK cluster is created. For more information, see [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md).

## Configure log collection by using YAML templates

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Serverless Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column.

4.  In the left-side navigation pane, click **Workloads**.

5.  Click the **Deployments** tab. In the upper-right corner of the page, click **Create from Template**.

6.  Create a custom template and copy the following code block into the template.

    The syntax of the YAML template is the same as that of Kubernetes. Logs are collected based on the environment variables that are specified in the env section. To collect log files, you must also specify the volumeMounts and volumes parameters. The following code block shows how to configure log collection for a Deployment:

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      labels:
        app: alpine
      name: alpine
    spec:
      replicas: 2
      selector:
        matchLabels:
          app: alpine
      template:
        metadata:
          labels:
            app: alpine
        spec:
          containers:
          - image: alpine
            imagePullPolicy: Always
            args:
            - ping
            - 127.0.0.1
            name: alpine
            env:
            ######### Specify environment variables ###########
            ######### Specify a Log Service project. If you use the default Log Service project for the cluster, this variable is optional. ###########
            - name: aliyun_logs_test-stdout_project
              value: k8s-log-xxx
            - name: aliyun_logs_test-file_project
              value: k8s-log-xxx
            ######### Specify a machine group. If you use the default machine group of the default Log Service project for the cluster, this variable is optional. ###########
            - name: aliyun_logs_test-stdout_machinegroup
              value: k8s-group-app-alpine
            - name: aliyun_logs_test-file_machinegroup
              value: k8s-group-app-alpine
            ######### Specify the Logstore that is used to store the collected stout and stderr files. In this example, the test-stdout Logstore is used.###########
            - name: aliyun_logs_test-stdout
              value: stdout
            ######### Specify the Logstore that is used to store the log files that are collected from the /log/*.log directory. In this example, the test-file Logstore is used.###########
            ######### Note: Except for stout and stderr files, log files must be collected from directories that are mounted with emptyDir volumes. Otherwise, log files cannot be collected.###########
            ######### Note: In this example, the /log/ directory must be mounted with an emptyDir volume, as shown in the following code.###########
            - name: aliyun_logs_test-file
              value: /log/*.log
            ######### The retention period for logs that are collected to a Logstore. This variable takes effect for only the specified Logstore. ###########
            - name: aliyun_logs_test-stdout_ttl
              value: "7"
            ######### The number of shards in a Logstore. This variable takes effect for only the specified Logstore. ###########
            - name: aliyun_logs_test-stdout_shard
              value: "2"
            #################################
            ######### Configure the volumeMounts and volumes parameters. An emptyDir volume is mounted to the specified directory.#######
            volumeMounts:
            - name: volume-sls
              mountPath: /log
          volumes:
          - name: volume-sls
            emptyDir: {}
    ```

    Configure the following variables in order based on your requirements:

    -   Configure log collection by specifying environment variables. Make sure that all specified environment variables are prefixed with `aliyun_logs_`. Configure log collection in the following format:

        ```
        - name: aliyun_logs_{Logstore name}
          value: {Log path}
        ```

        In the preceding YAML template, two environment variables are added to the collection configuration. Environment variable `aliyun_logs_log-stdout` indicates that a Logstore named log-stdout is created to store the stdout files from containers.

        **Note:** The Logstore name cannot contain underscores \(\_\) and can contain hyphens \(-\).

    -   If you specify a path to collect log files, you must add the volumeMounts parameter.

        The preceding YAML template specifies that log files are collected from the /log/\*.log directory. Therefore, the volumeMounts parameter is added and set to /log.

7.  After you configure the YAML template, click **Create** to apply the configurations to the ASK cluster.

    After the deployment is completed, you can run the following command to query the state of pods.

    ```
    NAME                      READY     STATUS    RESTARTS   AGE       IP             NODE
    alpine-76d978dbdd-gznk6   1/1       Running   0          21m       10.1.XX.XX   viking-c619c41329e624975a7bb50527180****
    alpine-76d978dbdd-vb9fv   1/1       Running   0          21m       10.1.XX.XX   viking-c619c41329e624975a7bb50527180****
    ```


## View collected logs

1.  Log on to the [Log Service console](https://sls.console.aliyun.com).

2.  In the **Projects** section, click the project that is associated with the Kubernetes cluster to go to the **Logstores** tab. By default, the project name follows the format of k8s-log-\{Kubernetes cluster ID\}.

3.  In the Logstore list, find the Logstore that is specified in the collection configuration, click ![Navigation icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0397297951/p57137.png), and then select **Search & Analysis** from the drop-down list.

    In this example, find the `test-stdout` Logstore, click ![Navigation icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0397297951/p57137.png), and then select **Search & Analysis** from the drop-down list. On the page that appears, you can check the `stdout` logs that are collected from related elastic container instances \(ECIs\).


