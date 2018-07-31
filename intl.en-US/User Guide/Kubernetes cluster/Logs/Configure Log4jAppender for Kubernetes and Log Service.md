# Configure Log4jAppender for Kubernetes and Log Service {#concept_eky_j3b_wdb .concept}

Log4j is an open-source project of Apache, which consists of three important components: log level, log output destination, and log output format. By configuring Log4jAppender, you can set the log output destination to console, file, GUI component, socket server, NT event recorder, or UNIX Syslog daemon.

This document introduces how to configure a YAML file to output Alibaba Cloud Container Service Kubernetes cluster logs to Alibaba Cloud Log Service, without modifying the application codes. In this document, deploy a sample API application in the Kubernetes cluster for demonstration.

## Prerequisites {#section_x4h_43b_wdb .section}

-   You have activated Container Service and created a Kubernetes cluster.

    In this example, create a Kubernetes cluster in the region of China East 1 \(Hangzhou\).

-   Enable AccessKey or Resource Access Management \(RAM\). Make sure you have sufficient access permissions. Use the AccessKey in this example.

## Step 1 Configure Log4jAppender in Alibaba Cloud Log Service {#section_y2l_p3b_wdb .section}

1.  Log on to the[Log Service console](https://sls.console.aliyun.com/).
2.  On the Project List page, click **Create Project** in the upper-right corner. Complete the configurations and then click **Confirm** to create the project.

    In this example, create a project named k8s-log4j and select the same region \(China East 1 \(Hangzhou\)\) as the Kubernetes cluster.

    **Note:** Generally, create a Log Service project in the same region as the Kubernetes cluster. When the Kubernetes cluster and Log Service project are in the same region, log data is transmitted by using the intranet, which saves the Internet bandwidth cost and time of data transmission because of different regions, and implements the best practice of real-time collection and quick query.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6944/15330267944689_en-US.png)

3.  After being created, the project k8s-log4j is displayed on the Project List page. Click the project name.
4.  The Logstore List page appears. Click **Create** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6944/15330267944690_en-US.png)

5.  Complete the configurations and then click **Confirm**.

    In this example, create a Logstore named k8s-logstore.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6944/15330267954691_en-US.png)

6.  Then, a dialog box asking you to use the data import wizard appears.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6944/15330267954692_en-US.png)

7.  Click Data Import Wizard. In the Select Data Source step, select log4jAppender under **Other Sources** and then complete the configurations as instructed on the page.

    Use the default configurations in this example. Configure the settings according to the specific scenarios of log data.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6944/15330267954693_en-US.png)


## Step 2 Configure Log4jAppender in the Kubernetes cluster {#section_wbv_n3b_wdb .section}

In this example, use the sample YAML files [demo-deployment](https://github.com/brucewu-fly/log4j-appender-demo-spring-boot/blob/master/k8s/demo-deployment.yaml) and [demo-service](https://github.com/brucewu-fly/log4j-appender-demo-spring-boot/blob/master/k8s/demo-service.yaml) for demonstration.

1.  Connect to your Kubernetes cluster.

    For more information, see [Access Kubernetes clusters by using SSH](intl.en-US/User Guide/Kubernetes cluster/Clusters/Access Kubernetes clusters by using SSH.md#) or [Connect to a Kubernetes cluster by using kubectl](intl.en-US/User Guide/Kubernetes cluster/Clusters/Connect to a Kubernetes cluster by using kubectl.md#).

2.  Obtain the demo-deployment.yaml file and configure the environment variable `JAVA_OPTS` to collect logs from the Kubernetes cluster.

    The sample orchestration of the demo-deployment.yaml file is as follows:

    ```
    apiVersion: apps/v1beta2
    kind: Deployment
    metadata:
      name: log4j-appender-demo-spring-boot
      labels:
        app: log4j-appender
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: log4j-appender
      template:
        metadata:
        labels:
          app: log4j-appender
      spec:
        containers:
        - name: log4j-appender-demo-spring-boot
          image: registry.cn-hangzhou.aliyuncs.com/jaegertracing/log4j-appender-demo-spring-boot:0.0.2
          env:
          - name: JAVA_OPTS ##Note
            value: "-Dproject={your_project} -Dlogstore={your_logstore} -Dendpoint={your_endpoint} -Daccess_key_id={your_access_key_id} -Daccess_key={your_access_key_secret}"
          ports:
          - containerPort: 8080
    ```

    Wherein:

    -   `-Dproject`: The name of the used Alibaba Cloud Log Service project. In this example, it is k8s-log4j.
    -   `-Dlogstore`: The name of the used Alibaba Cloud Log Service Logstore. In this example, it is k8s-logstore.
    -   `-Dendpoint`: The service endpoint of Log Service. You must configure your service endpoint according to the region where the Log Service project resides. For more information, see [Service endpoint](https://www.alibabacloud.com/help/zh/doc-detail/29008.htm). In this example, it is cn-hangzhou.log.aliyuncs.com.
    -   `-Daccess_key_id`: Your AccessKey ID.
    -   `-Daccess_key`: Your AccessKey Secret.
3.  Run the following command in the command line to create the deployment:

    ```
    kubectl create -f demo-deployment.yaml
    ```

4.  Obtain the demo-service.yaml file and run the following command to create the service.

    No need to modify the configurations in the demo-service.yaml file.

    ```
    kubectl create -f demo-service.yaml
    ```


## Step 3 Test to generate Kubernetes cluster logs {#section_kcv_n3b_wdb .section}

You can run the `kubectl get`  command to view the deployment status of the resource object. Wait until the deployment and the service are successfully deployed. Then, run the `kubectl get svc` command to view the external access IP of the service, that is, the EXTERNAL-IP.

```
$ kubectl get svc
NAME TYPE CLUSTER-IP EXTERNAL-IP PORT(S) AGE
log4j-appender-demo-spring-boot-svc LoadBalancer 172.21. XX.XX 120.55. XXX.XXX 8080:30398/TCP 1h
```

In this example, test to generate Kubernetes cluster logs by running the `login` command, wherein, `K8S_SERVICE_IP` is the `EXTERNAL-IP`.

**Note:** See  [GitHub log4j-appender-demo](https://github.com/brucewu-fly/log4j-appender-demo-spring-boot) to view the complete collection of APIs.

```
curl http://${K8S_SERVICE_IP}:8080/login? name=bruce
```

## Step 4 View logs in Alibaba Cloud Log Service {#section_ncv_n3b_wdb .section}

Log on to the [Log Service console](https://sls.console.aliyun.com/).

Click the project name and click **Search** at the right of the Logstore k8s-logstore to view the output logs of the Kubernetes cluster.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6944/15330267954694_en-US.png)

The output content of the log corresponds to the preceding command. This example demonstrates how to output the logs of the sample application to Alibaba Cloud Log Service. By completing the preceding steps, you can configure Log4JAppender in Alibaba Cloud and implement advanced functions such as collecting logs in real time, filtering data, and querying logs by using Alibaba Cloud Log Service.

