# Configure Log4jAppender for Kubernetes and Log Service

This topic describes how to configure a YAML file to export the logs of a Container Service for Kubernetes \(ACK\) cluster to Log Service without modifying the application. The following procedures demonstrate how to configure Log4jAppender by deploying an API-based application in an ACK cluster.

-   ACK is activated and an ACK cluster is created.

    In this example, a managed ACK cluster is created in the China \(Hangzhou\) region.

-   An AccessKey pair is created or Resource Access Management \(RAM\) is activated. Make sure that the required permissions are granted. In this example, an AccessKey pair is created.

Log4j is an open source project of Apache. Log4j consists of three components: log level, log output destination, and log output format. You can configure Log4Appender to export log data to the console, a log file, a GUI component, a socket server, an NT event viewer, or a UNIX syslog daemon.

1.  Configure Log4jAppender in Log Service.

    1.  Create a project in Log Service.

        In this example, a project named k8s-log4j is created in the China \(Hangzhou\) region where the ACK cluster is deployed. For more information, see [Create a project](/intl.en-US/Data Collection/Preparation/Manage a project.md).

        **Note:** We recommend that you create a project in the region where the ACK cluster is deployed. When a Log Service project and an ACK cluster are deployed in the same region, log data is transmitted through the internal network. This saves the Internet bandwidth cost, reduces the time of cross-region transmission, and ensures fast log collection and retrieval.

        ![Create a Log Service project](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5055359951/p10630.png)

    2.  Create a Logstore for the k8s-log4j project.

        In this example, a Logstore named k8s-logstore is created. For more information, see [Create a Logstore](/intl.en-US/Data Collection/Preparation/Manage a Logstore.md).

        ![Configure the Logstore](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5055359951/p10632.png)

    3.  After the k8s-logstore Logstore is created, a dialog box appears. It provides instructions about how to use the Data Import wizard.

        ![Configure the Data Import wizard](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5055359951/p10633.png)

    4.  Select log4jAppender under **Custom Data**, and then configure the settings by performing the steps that are provided on the page.

        In this example, log4jAppender is configured with the default settings. You can also customize the settings to meet your business requirements.

        ![Custom data](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1155359951/p10634.png)

2.  Configure the k8s-log4j project for the ACK cluster.

    In this example, the [demo-deployment](https://github.com/brucewu-fly/log4j-appender-demo-spring-boot/blob/master/k8s/demo-deployment.yaml) and [demo-Service](https://github.com/brucewu-fly/log4j-appender-demo-spring-boot/blob/master/k8s/demo-service.yaml) files are used.

    1.  Connect to your ACK cluster.

        For more information, see [Use SSH to connect to Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use SSH to connect to an ACK cluster.md) or [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

    2.  Obtain the demo-deployment.yaml file and configure the `JAVA_OPTS` environment variable.

        The following code block is a sample template of the demo-deployment.yaml file:

        ```
        apiVersion: apps/v1
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
              - name: JAVA_OPTS                ## Pay attention to the following fields
                value: "-Dproject={your_project} -Dlogstore={your_logstore} -Dendpoint={your_endpoint} -Daccess_key_id={your_access_key_id} -Daccess_key={your_access_key_secret}"
              ports:
              - containerPort: 8080
        ```

        **Note:** Where:

        -   `-Dproject`: the name of your Log Service project. In this example, the project name is k8s-log4j.
        -   `-DlogStore`: the name of your Logstore in Log Service. In this example, the Logstore name is k8s-logstore.
        -   `-Dendpoint`: the service endpoint of Log Service. You must configure the service endpoint based on the region where the Log Service project is deployed. For more information, see[Service endpoints](https://www.alibabacloud.com/help/zh/doc-detail/29008.htm). In this example, the service endpoint is cn-hangzhou.log.aliyuncs.com.
        -   `-Daccess_key_id`: your AccessKey ID.
        -   `-Daccess_key`: your AccessKey secret.
    3.  Run the following command to create a Deployment:

        ```
        kubectl create -f demo-deployment.yaml
        ```

    4.  Obtain the demo-service.yaml file and run the following command to create a Service:

        You do not need to modify the configurations in the demo-service.yaml file.

        ```
        kubectl create -f demo-service.yaml
        ```

3.  Test to generate ACK cluster logs.

    You can run the `kubectl get` command to view the deployment status of the related resource objects. After the Deployment and Service are deployed, run the `kubectl get svc` command to check the external IP address of the Service, EXTERNAL-IP.

    Command:

    ```
    kubectl get svc
    ```

    Output:

    ```
    NAME                      TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    log4j-appender-demo-spring-boot-svc   LoadBalancer   172.21.XX.XX   120.55.XXX.XXX   8080:30398/TCP   1h
    ```

    In this example, you can run the `login` command to generate Kubernetes cluster logs. wherein, `K8 S_SERVICE_IP` is the `EXTERNAL-IP`.

    **Note:** For a complete list of API operations, visit [GitHub log4j-appender-demo](https://github.com/brucewu-fly/log4j-appender-demo-spring-boot).

    ```
    curl http://${K8S_SERVICE_IP}:8080/login? name=bruce
    ```

4.  View logs in Log Service

    1.  In the **Projects** section of the Log Service console, find and click the k8s-log4j project to go to the details page of the project.

    2.  Click the ![Icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5055359951/p55777.png) icon on the right side of the k8s-logstore Logstore and then click **Search & Analysis** to view the logs of the Kubernetes cluster.


