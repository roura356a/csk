---
keyword: [collect log files, Log Service]
---

# Collect log files by using Log Service

This topic describes how to use Log Service to collect stdout files and log files from containers in serverless Kubernetes \(ASK\) clusters.

An ASK cluster is created. For more information, see [t16481.md\#](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md).

## Configure log collection by using YAML templates

1.  On the **Deployments** page, click **Create from YAML** in the upper-right corner.

2.  Create a custom template and copy the following code block into the template.

    The syntax of the YAML template is the same as that of Kubernetes. Log files are collected based on the environment variables that are specified in the env section. To collect log files, you must also specify the volumeMounts and volumes parameters. The following is an example of a Deployment for collecting log files:

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
            ######### Configure environment variables ###########
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
            ######### Specify a Logstore that is used to store the collected stout and stderr files. In this example, the test-stdout Logstore is used.###########
            - name: aliyun_logs_test-stdout
              value: stdout
            ######### Specify a Logstore that is used to store the log files collected from the /log/*.log directory. In this example, the test-file Logstore is used.###########
            ######### Note: Except for stout and stderr files, log files must be collected from directories that are mounted with emptyDir volumes. Otherwise, log files cannot be collected.###########
            ######### Note: In this example, the /log/ directory must be mounted with an emptyDir volume, as shown in the following code.###########
            - name: aliyun_logs_test-file
              value: /log/*.log
            ######### The retention period of log files that are collected to a Logstore. This variable applies to only the specified Logstore. ###########
            - name: aliyun_logs_test-stdout_ttl
              value: "7"
            ######### The number of shards in a Logstore. This variable applies to only the specified Logstore. ###########
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

    Configure the following variables in sequence based on your requirements:

    -   Configure log collection by specifying environment variables. Make sure that all specified environment variables use the `aliyun_logs_` prefix. Add log collection configurations in the following format:

        ```
        - name: aliyun_logs_{Logstore name}
          value: {Log file path}
        ```

        In the preceding YAML template, two environment variables are added to the log collection configuration. Environment variable `aliyun_logs_log-stdout` indicates that a Logstore named log-stdout is created to store the `stdout` files collected from containers.

        **Note:** The name of the Logstore cannot contain underscores \(\_\). You can use hyphens \(-\) instead.

    -   If you specify a log file path to collect log files other than the `stdout` files, you must configure `volumeMounts`.

        The preceding YAML template specifies that log files are collected from the /log/\*.log directory. Therefore, the volumeMounts parameter is specified and set to /log of `volumnMounts`.

3.  After you modify the YAML template, click **Create** to submit the configurations.

    After the Deployment is created, you can run the following command to query the states of pods:

    ```
    kubectl get Pods
    ```

    Expected output:

    ```
    NAME                      READY     STATUS    RESTARTS   AGE       IP             NODE
    alpine-76d978dbdd-gznk6   1/1       Running   0          21m       10.1.XX.XX   viking-c619c41329e624975a7bb50527180****
    alpine-76d978dbdd-vb9fv   1/1       Running   0          21m       10.1.XX.XX   viking-c619c41329e624975a7bb50527180****
    ```


## View log files

1.  In the Logstore list, find the Logstore that is specified when you configure log collection, click the ![Navigation icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0397297951/p57137.png) icon, and then select **Search & Analysis** from the drop-down list.

    In this example, find the `test-stdout` Logstore, click the ![Navigation icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0397297951/p57137.png) icon, and then select **Search & Analysis** from the drop-down list. On the page that appears, you can check the `stdout` files that are collected from elastic container instances.


