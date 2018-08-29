# 为 Kubernetes 和日志服务配置 Log4JAppender {#concept_eky_j3b_wdb .concept}

Log4j 是 Apache 的一个开放源代码项目。Log4j 由三个重要组件构成：日志信息的优先级、日志信息的输出目的地、日志信息的输出格式。通过配置 Log4jAppender，您可以控制日志信息输送的目的地是控制台、文件、GUI 组件、甚至是套接口服务器、NT 的事件记录器、UNIX Syslog 守护进程等。

本文介绍在不需要修改应用代码的前提下，通过配置一个 yaml 文件，将阿里云容器服务 Kubernetes 集群中产生的日志输出到阿里云日志服务。此外，通过在 Kubernetes 集群上部署一个示例 API 程序，来进行演示。

## 前提条件 {#section_x4h_43b_wdb .section}

-   您已经开通容器服务，并且创建了 Kubernetes 集群。

    本示例中，创建的 Kubernetes 集群位于华东 1 地域。

-   启用 AccessKey 或 RAM，确保有足够的访问权限。本例中使用 AccessKey。

## 步骤 1 在阿里云日志服务上配置 Log4jAppender {#section_y2l_p3b_wdb .section}

1.  登录 [日志服务管理控制台](https://sls.console.aliyun.com/)。
2.  单击页面右上角的**创建 Project**，填写 Project 的基本信息并单击**确认**进行创建。

    本示例创建一个名为 k8s-log4j，与 Kubernetes 集群位于同一地域（华东 1）的 Project。

    **说明：** 在配置时，一般会使用与 Kubernetes 集群位于同一地域的日志服务 Project。因为当 Kubernetes 集群和日志服务 Project 位于同一地域时，日志数据会通过内网进行传输，从而避免了因地域不一致而导致的数据传输外网带宽费用和耗时，从而实现实时采集、快速检索的最佳实践。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16697/153552337110630_zh-CN.png)

3.  创建完成后，k8s-log4j 出现在 project 列表下，单击该 project 名称，进入 project 详情页面。
4.  默认进入日志库页面，单击右上角的**创建**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16697/153552337110631_zh-CN.png)

5.  填写日志库配置信息并单击**确认**。

    本示例创建名为 k8s-logstore 的日志库。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16697/153552337110632_zh-CN.png)

6.  创建完毕后，页面会提示您创建数据接入向导。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16697/153552337110633_zh-CN.png)

7.  选择**自定义数据**下的log4jAppender，根据页面引导进行配置。

    本示例使用了默认配置，您可根据日志数据的具体使用场景，进行相应的配置。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16697/153552337110634_zh-CN.png)


## 步骤 2 在 Kubernetes 集群中配置 log4j {#section_wbv_n3b_wdb .section}

本示例使用 [demo-deployment](https://github.com/brucewu-fly/log4j-appender-demo-spring-boot/blob/master/k8s/demo-deployment.yaml) 和 [demo-Service](https://github.com/brucewu-fly/log4j-appender-demo-spring-boot/blob/master/k8s/demo-service.yaml) 示例 yaml 文件进行演示。

1.  连接到您的 Kubernetes 集群。

    具体操作参见[SSH访问Kubernetes集群](intl.zh-CN/用户指南/Kubernetes 集群/集群管理/SSH访问Kubernetes集群.md#) 或 [通过 kubectl 连接 Kubernetes 集群](intl.zh-CN/用户指南/Kubernetes 集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)。

2.  获取demo-deployment.yaml文件并配置环境变量 `JAVA_OPTS` 设置 Kubernetes 集群日志的采集。

    demo-deployment.yaml 文件的示例编排如下。

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
          - name: JAVA_OPTS                ##注意
            value: "-Dproject={your_project} -Dlogstore={your_logstore} -Dendpoint={your_endpoint} -Daccess_key_id={your_access_key_id} -Daccess_key={your_access_key_secret}"
          ports:
          - containerPort: 8080
    ```

    其中：

    -   `-Dproject`：您所使用的阿里云日志服务 Project 的名称。本示例中为 k8s-log4j。
    -   `-Dlogstore`：您所使用的阿里云日志服务 Logstore 的名称。本示例中为 k8s-logstore。
    -   `-Dendpoint`：日志服务的服务入口，用户需要根据日志 Project 所属的地域，配置自己的服务入口，参见[服务入口](https://www.alibabacloud.com/help/zh/doc-detail/29008.htm)进行查询。本示例中为 cn-hangzhou.log.aliyuncs.com。
    -   `-Daccess_key_id`：您的 AccessKey ID。
    -   `-Daccess_key`：您的 AccessKey Secret。
3.  在命令行中执行以下命令，创建 deployment。

    ```
    kubectl create -f demo-deployment.yaml
    ```

4.  获取demo-Service.yaml文件，并运行以下命令创建 service。

    您不需要修改demo-Service.yaml中的配置。

    ```
    kubectl create -f demo-service.yaml
    ```


## 步骤 3 测试生成 Kubernetes 集群日志 {#section_kcv_n3b_wdb .section}

您可以使用 `kubectl get` 命令查看资源对象部署状况，等待 deployment 和 service 部署成功后，执行 `kubectl get svc` 查看 service 的外部访问 IP，即 EXTERNAL-IP。

```
$ kubectl get svc
NAME                      TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
log4j-appender-demo-spring-boot-svc   LoadBalancer   172.21.XX.XX   120.55.XXX.XXX   8080:30398/TCP   1h
```

在本示例中，通过运行`login`命令来测试生成 Kubernetes 集群日志。其中 `K8S_SERVICE_IP` 即为 `EXTERNAL-IP`。

**说明：** 您可以在 [GitHub log4j-appender-demo](https://github.com/brucewu-fly/log4j-appender-demo-spring-boot) 中查看完整的 API 集合。

```
curl http://${K8S_SERVICE_IP}:8080/login?name=bruce
```

## 步骤 4 在阿里云日志服务中查看日志 {#section_ncv_n3b_wdb .section}

登录 [日志服务管理控制台](https://sls.console.aliyun.com/)。

进入对应的 Project 的详情页面，选择对应的日志库 k8s-logstore，并单击右侧的**查询分析 - 查询**，查看 Kubernetes 集群输出的日志，如下所示。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16697/153552337110635_zh-CN.png)

日志的输出内容对应上面的命令。本示例演示了将示例应用产生的日志输出到阿里云日志服务的操作。通过这些操作，您可以在阿里云上配置 Log4JAppender，并通过阿里云日志服务，实现日志实时搜集、数据过滤、检索等高级功能。

