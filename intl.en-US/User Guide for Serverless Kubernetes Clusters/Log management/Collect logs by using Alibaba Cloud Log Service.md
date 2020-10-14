# Collect logs by using Alibaba Cloud Log Service

This topic describes how to use Alibaba Cloud Log Service to collect the standard output and log files of containers in serverless Kubernetes clusters.

A serverless Kubernetes cluster is created. For more information, see [Create a serverless Kubernetes cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create a serverless Kubernetes cluster.md).

## Use a YAML template to configure log collection

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Serverless Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column.

4.  In the left-side navigation pane, click **Workload**.

5.  In the left-side navigation pane, choose Applications \> **Deployments**. In the upper-right corner of the Deployments page, click **Create from Template**.

6.  The syntax of a YAML template is the same as that of Kubernetes.

    Configure log collection by specifying environment variables in the env field. To collect log files, you also need to specify the volumeMounts and volumes fields. The following example shows how to configure log collection for a deployment:

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
            - 127.0.XX.XX
            name: alpine
            env:
            ######### Specify environment variables ###########
            ######### Specify a project. If you use the default project of the Kubernetes cluster, this parameter is optional. ###########
            - name: aliyun_logs_project
              value: k8s-xxx
            ######### Specify a machine group. If you use the default machine group of the default project of the Kubernetes cluster, this parameter is optional. ###########
            ######### We recommend that you do not use the default machine group of the Kubernetes cluster. Customize a machine group by only specifying the name. After you specify the name, Elastic Container Instance (ECI) creates and configures a machine group for you. ###########
            - name: aliyun_logs_machinegroup
              value: k8s-group-app-alpine
            ######### The value of aliyun_logs_machinegroup must be unique across clusters. If you set this parameter to the same value for two containers in different clusters, the two containers have the same custom ID. We recommend that you set the value in the format of {Cluster or environment name}-{Application name}, for example, test-app and prod-app2. ###########
            - name: aliyun_logs_log-stdout
              value: stdout
            - name: aliyun_logs_test-file
              value: /log/*.log
            ######### The retention period of logs in a specified Logstore. ###########
            - name: aliyun_logs_test-stdout_ttl
              value: "7"
            ######### The number of log partitions in a specified Logstore. ###########
            - name: aliyun_logs_test-stdout_shard
              value: "2"
            #################################
            ######### Configure volumeMounts and volumes. #######
            volumeMounts:
            - name: volume-sls
              mountPath: /log
          volumes:
          - name: volumn-sls
            emptyDir: {}
    ```

    Configure the following parameters in order based on your needs:

    -   Configure log collection by specifying environment variables. Ensure that all the environment variables related to log collection are prefixed with `aliyun_logs_`. Configure log collection in the following format:

        ```
        - name: aliyun_logs_{Logstore name}
          value: {Log path}
        ```

        In the preceding example, environment variables are set for two log collection tasks. `aliyun_logs_log-stdout` and stdout instruct the system to create a Logstore named log-stdout and collect standard output of the container to this Logstore.

        **Note:** A Logstore name cannot contain underscores \(\_\). You can use hyphens \(-\) instead.

    -   If you specify a path to collect log files apart from the standard output, you must add the volumeMounts field.

        In the preceding example, the volumeMounts field is added and the value of mountPath is /log. This configuration instructs the system to collect /log/\*.log files.

7.  After you configure the YAML template, click **Create** to apply the configurations to the serverless Kubernetes cluster.

    After the deployment is complete, you can run the corresponding command to view the pod status.

    ```
    NAME                      READY     STATUS    RESTARTS   AGE       IP             NODE
    alpine-76d978dbdd-gznk6   1/1       Running   0          21m       10.1.XX.XX   viking-c619c41329e624975a7bb50527180****
    alpine-76d978dbdd-vb9fv   1/1       Running   0          21m       10.1.XX.XX   viking-c619c41329e624975a7bb50527180****
    ```


## View logs

1.  Log on to the [Log Service console](https://sls.console.aliyun.com).

2.  Select the target project. By default, the project ID is k8s-log-\{Cluster ID\}.

3.  In the Logstore list, find the Logstore specified in the log collection configuration, click ![ ](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0397297951/p57137.png), and select **Search & Analysis** from the drop-down list.

    In this example, find the `test-stdout` Logstore, click ![ ](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0397297951/p57137.png), and select **Search & Analysis** from the drop-down list. You can check the collected `standard output` of the ECI.


