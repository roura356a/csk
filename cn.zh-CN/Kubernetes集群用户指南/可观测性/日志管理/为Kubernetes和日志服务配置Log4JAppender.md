# 为Kubernetes和日志服务配置Log4JAppender

本文介绍在不需要修改应用代码的前提下，通过配置一个YAML文件，将阿里云容器服务Kubernetes集群中产生的日志输出到阿里云日志服务。此外，通过在Kubernetes集群上部署一个示例API程序，来进行演示。

-   您已经开通容器服务，并且创建了Kubernetes集群。

    本示例中，创建的Kubernetes集群位于华东1（杭州）。

-   启用AccessKey或RAM，确保有足够的访问权限。本例中使用AccessKey。

Log4j是Apache的一个开放源代码项目。Log4j由三个重要组件构成：日志信息的优先级、日志信息的输出目的地、日志信息的输出格式。通过配置Log4jAppender，您可以控制日志信息输送的目的地是控制台、文件、GUI组件，甚至是套接口服务器、NT的事件记录器、UNIX Syslog守护进程等。

1.  在阿里云日志服务上配置Log4jAppender。

    1.  创建一个日志服务Project。

        本示例创建一个名为k8s-log4j、与Kubernetes集群位于同一地域（华东 1）的Project。具体操作，请参见[创建Project](/cn.zh-CN/数据采集/准备工作/管理Project.md)。

        **说明：** 在配置时，一般会使用与Kubernetes集群位于同一地域的日志服务Project。因为当Kubernetes集群和日志服务Project位于同一地域时，日志数据会通过内网进行传输，从而避免了因地域不一致而导致的数据传输外网带宽费用和耗时，从而实现实时采集、快速检索的最佳实践。

        ![创建project](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2985659951/p10630.png)

    2.  为k8s-log4j的Project创建一个Logstore。

        本示例创建名为k8s-logstore的日志库。具体操作，请参见[创建Logstore](/cn.zh-CN/数据采集/准备工作/管理Logstore.md)。

        ![配置日志库信息](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2985659951/p10632.png)

    3.  在k8s-logstore创建完成后，页面会提示您创建数据接入向导。

        ![创建数据接入向导](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2985659951/p10633.png)

    4.  选择**自定义代码**下的log4jAppender，根据页面引导进行配置。

        本示例使用了默认配置，您可根据日志数据的具体使用场景，进行相应的配置。

        ![自定义数据](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0095659951/p10634.png)

2.  在Kubernetes集群中配置log4j。

    本示例使用[demo-deployment](https://github.com/brucewu-fly/log4j-appender-demo-spring-boot/blob/master/k8s/demo-deployment.yaml)和[demo-Service](https://github.com/brucewu-fly/log4j-appender-demo-spring-boot/blob/master/k8s/demo-service.yaml)示例YAML文件进行演示。

    1.  连接到您的Kubernetes集群。

        具体操作参见[SSH访问Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/连接集群/通过SSH访问Kubernetes集群.md)或[通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/连接集群/通过kubectl连接Kubernetes集群.md)。

    2.  获取demo-deployment.yaml文件并配置环境变量`JAVA_OPTS`。

        demo-deployment.yaml文件的示例编排如下。

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
                  - name: JAVA_OPTS                ##注意
                    value: "-Dproject={your_project} -Dlogstore={your_logstore} -Dendpoint={your_endpoint} -Daccess_key_id={your_access_key_id} -Daccess_key={your_access_key_secret}"
                ports:
                - containerPort: 8080
        ```

        **说明：** 其中：

        -   `-Dproject`：您所使用的阿里云日志服务Project的名称。本示例中为k8s-log4j。
        -   `-Dlogstore`：您所使用的阿里云日志服务Logstore的名称。本示例中为k8s-logstore。
        -   `-Dendpoint`：日志服务的服务入口，用户需要根据日志Project所属的地域，配置自己的服务入口，参见[服务入口](https://help.aliyun.com/document_detail/29008.html)进行查询。本示例中为cn-hangzhou.log.aliyuncs.com。
        -   `-Daccess_key_id`：您的AccessKey ID。
        -   `-Daccess_key`：您的AccessKey Secret。
    3.  在命令行中执行以下命令，创建deployment。

        ```
        kubectl create -f demo-deployment.yaml
        ```

    4.  获取demo-Service.yaml文件，并运行以下命令创建service。

        您不需要修改demo-Service.yaml中的配置。

        ```
        kubectl create -f demo-service.yaml
        ```

3.  测试生成Kubernetes集群日志。

    您可以使用`kubectl get`命令查看资源对象部署状况，等待deployment和service部署成功后，执行`kubectl get svc`查看service的外部访问IP，即EXTERNAL-IP。

    命令如下：

    ```
    kubectl get svc
    ```

    结果如下：

    ```
    NAME                      TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    log4j-appender-demo-spring-boot-svc   LoadBalancer   172.21.XX.XX   120.55.XXX.XXX   8080:30398/TCP   1h
    ```

    在本示例中，通过运行`login`命令来测试生成Kubernetes集群日志。其中`K8S_SERVICE_IP`即为`EXTERNAL-IP`。

    **说明：** 您可以在[GitHub log4j-appender-demo](https://github.com/brucewu-fly/log4j-appender-demo-spring-boot)中查看完整的API集合。

    ```
    curl http://${K8S_SERVICE_IP}:8080/login?name=bruce
    ```

4.  在阿里云日志服务中查看日志。

    1.  在**Project列表**中，单击目标Project，进入对应的Project详情页面。

    2.  在对应的日志库k8s-logstore右侧的![图标](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2985659951/p55777.png)图标，选择**查询分析**，查看Kubernetes集群输出的日志，如下所示。

        ![查询分析](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2985659951/p55775.png)


