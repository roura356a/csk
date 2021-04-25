---
keyword: [Log-pilot, 日志采集, ES, k8s]
---

# 利用开源组件Log-pilot搭建Kubernetes日志解决方案

本文档介绍如何使用开源的Docker日志收集工具Log-pilot，结合Elasticsearch和Kibana等工具，形成一套适用于Kubernetes环境下的一站式日志解决方案。

-   您已经开通容器服务，并创建了一个Kubernetes集群。具体步骤，请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。

    本示例中，创建的Kubernetes集群位于华东1地域。

-   请务必确保到Elasticsearch集群网络的可连通性。

由于以下容器本身特性和现有日志采集工具的缺陷，开发者在收集Kubernetes分布式集群日志时常常遇到困扰：

-   容器本身特性：
    -   采集目标多：容器本身的特性导致采集目标多，需要采集容器内日志、容器stdout。对于容器内部的文件日志采集，现在并没有一个很好的工具能够动态发现采集。针对每种数据源都有对应的采集软件，但缺乏一站式的工具。
    -   弹性伸缩难：Kubernetes是分布式的集群，服务、环境的弹性伸缩对于日志采集带来了很大的困难，无法像传统虚拟机环境下那样，事先配置好日志的采集路径等信息，采集的动态性以及数据完整性是非常大的挑战。
-   现有日志工具的一些缺陷：
    -   缺乏动态配置的能力。目前的采集工具都需要事先手动配置好日志采集方式和路径等信息，因为它无法自动感知到容器的生命周期变化或者动态漂移，所以它无法动态地去配置。
    -   日志采集重复或丢失的问题。因为现在的一些采集工具通过tail的方式进行日志采集，这样可能导致日志丢失，例如采集工具在重启的过程中，而应用依然在写日志，那么就有可能导致这个窗口期的日志丢失。对于这种情况一般保守的做法默认往前多采集1 M日志或2 M的日志，那么这就又会可能引起日志采集重复的问题。
    -   未明确标记日志源。因为一个应用可能有很多个容器，输出的应用日志也是一样的，那么当将所有应用日志收集到统一日志存储后端时，在搜索日志的时候，您无法明确这条日志具体是哪一个节点上的哪一个应用容器产生的。

## Log-pilot介绍

Log-pilot是一个智能容器日志采集工具，它不仅能够高效便捷地将容器日志采集输出到多种存储日志后端，同时还能够动态地发现和采集容器内部的日志文件。

针对前面提出的日志采集难题，Log-pilot通过声明式配置实现强大的容器事件管理，可同时获取容器标准输出和内部文件日志，解决了动态伸缩问题，此外，Log-pilot具有自动发现机制、CheckPoint及句柄保持的机制、自动日志数据打标、有效应对动态配置、日志重复和丢失以及日志源标记等问题。

目前Log-pilot在Github完全开源，项目地址是[github.com/AliyunContainerService/log-pilot](https://github.com/AliyunContainerService/log-pilot)。您可以深入了解更多实现原理。

## 针对容器日志的声明式配置

Log-pilot支持容器事件管理，它能够动态地监听容器的事件变化，然后依据容器的标签来进行解析，生成日志采集配置文件，然后交由采集插件来进行日志采集。

在Kubernetes下，Log-pilot可以依据环境变量`aliyun_logs_$name = $path`动态地生成日志采集配置文件，其中包含两个变量：

-   $name是自定义的一个字符串，它在不同的场景下指代不同的含义，在本场景中，将日志采集到Elasticsearch的时候，这个$name表示的是Index。
-   $path，支持两种输入形式，stdout和容器内部日志文件的路径，分别对应日志标准输出和容器内的日志文件：
    -   第一种约定关键字stdout表示的是采集容器的标准输出日志，本例采集tomcat容器日志，那么通过配置标签`aliyun.logs.catalina=stdout`来采集tomcat标准输出日志。
    -   第二种是容器内部日志文件的路径，也支持通配符的方式，通过配置环境变量`aliyun_logs_access=/usr/local/tomcat/logs/*.log`来采集tomcat容器内部的日志。当然如果您不想使用aliyun这个关键字，Log-pilot也提供了环境变量PILOT\_LOG\_PREFIX可以指定自己的声明式日志配置前缀，例如`PILOT_LOG_PREFIX: "aliyun,custom"`。

此外，Log-pilot还支持多种日志解析格式，通过`aliyun_logs_$name_format=<format>`标签告诉Log-pilot在采集日志的时候，同时以哪种格式来解析日志记录，支持的格式包括：NONE、JSON、CSV、NGINX、APACHE2和REGXP。

Log-pilot同时支持自定义tag，我们可以在环境变量里配置`aliyun_logs_$name_tags="K1=V1,K2=V2"`，那么在采集日志的时候也会将K1=V1和K2=V2采集到容器的日志输出中。自定义tag可帮助您给日志产生的环境打上tag，方便进行日志统计、日志路由和日志过滤。

## 日志采集模式

本文档采用Node方式进行部署，通过在每台机器上部署一个Log-pilot实例，收集机器上所有Docker应用日志。

该方案跟在每个Pod中都部署一个logging容器的模式相比，最明显的优势就是占用资源较少，在集群规模比较大的情况下表现出的优势越明显，这也是社区推荐的一种模式。

![日志采集模式](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0985659951/p10636.png)

## 步骤一：部署Elasticsearch集群

**说明：** 阿里云Elasticsearch集群版本需低于7.x.x。有关如何部署Elasticsearch集群，请参见[t134282.dita\#task\_2277894](/intl.zh-CN/Elasticsearch/管理实例/创建阿里云Elasticsearch实例.md)。默认情况下，阿里云Elasticsearch集群当新增的文档发现没有索引时，不允许自动创建索引，而Log-pilot在自动采集容器日志时需要依据日志采集配置来自动创建文档索引，因此我们这里需要开启**自动创建索引**功能。

您可通过以下操作，开启**自动创建索引**功能。

1.  登录[阿里云Elasticsearch控制台](https://elasticsearch.console.aliyun.com/#/instances)。

2.  在Elasticsearch实例页面的实例列表中，单击**实例ID/名称**链接。

3.  单击左侧导航栏的**ES集群配置**，进入**ES集群配置**页面。在YML文件配置区域框右侧单击**修改配置**，弹出YAML文件配置页面。

4.  自动创建索引设置为**允许自动创建索引**，选中**该操作会重启实例，请确认后操作**。然后单击**确认**。


## 步骤二：部署Log-pilot组件

为降低节点的资源消耗，Log-pilot组件采用的是DaemonSet方式部署到每个集群的Node节点上。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

5.  选择**kube-system**作为**命名空间**。

6.  在无状态页面，选择**kube-system**命名空间，单击右上角的**使用YAML创建资源**。

7.  **示例模板**选择**自定义**，并将以下内容复制到**模板**中，单击**创建**。

    ```
    apiVersion: apps/v1
    kind: DaemonSet
    metadata:
      name: log-pilot
      labels:
        app: log-pilot
      # 设置期望部署的namespace。
      namespace: kube-system
    spec:
      selector:
        matchLabels:
          app: log-pilot
      updateStrategy:
        type: RollingUpdate
      template:
        metadata:
          labels:
            app: log-pilot
          annotations:
            scheduler.alpha.kubernetes.io/critical-pod: ''
        spec:
          # 是否允许部署到Master节点上tolerations。
          tolerations:
          - key: node-role.kubernetes.io/master
            effect: NoSchedule
          containers:
          - name: log-pilot
            # 版本请参考https://github.com/AliyunContainerService/log-pilot/releases。
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
              # 请确保集群到ES网络可达。
              - name: "ELASTICSEARCH_HOSTS"
                value: "{es_endpoint}:{es_port}"
              # 配置ES访问权限。
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

    |参数|描述|
    |--|--|
    |\{es\_endpoint\}|ES集群的访问地址。|
    |\{es\_port\}|    -   如果Kubernetes集群和部署的ES集群在同一个VPC下，则该地址为ES集群基本信息中的**内网地址**。
    -   如果Kubernetes集群和部署的ES集群不在同一个VPC下，则该地址为ES集群基本信息中的**公网地址**。 |
    |\{es\_username\}|访问ES集群的用户名。即为创建ES集群时，设置的用户名。|
    |\{es\_password\}|访问ES集群的用户密码。即为创建ES集群时，设置的用户密码。|

8.  在集群管理页左侧导航栏中，选择**工作负载** \> **守护进程集**。

    选择目标集群，命名空间为**kube-system**，看到下图所示时，表示运行正常。

    ![pilot](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1985659951/p133207.png)


**说明：** 您也可以[通过kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。使用上述YAML模板创建DaemonSet，并执行以下命令查看Log-pilot组件的运行状态。

```
~ kubectl apply -f log-pilot.yml
daemonset.extensions "log-pilot" created
~ kubectl -n kube-system get pod | grep log-pilot
log-pilot-458nj 1/1 Running 0 23s
log-pilot-8ld4n 1/1 Running 0 23s
log-pilot-b4kqv 1/1 Running 0 23s
log-pilot-gd588 1/1 Running 0 23s
log-pilot-k2ttk 1/1 Running 0 23s
```

## 步骤三：采集日志

这里以部署一个Tomcat为例，通过创建Deployment应用配置日志采集（配置方式同样适用StatefulSet），测试日志是否能正常采集、索引和显示。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

    **说明：** 目标集群必须和Log-pilot组件部署在同一集群下。

4.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

5.  选择目标**命名空间**，单击右上角的**使用YAML创建资源**。

6.  **示例模板**选择**自定义**，并将以下内容复制到**模板**中，单击**创建**。

    编排模板如下。

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: tomcat
    spec:
      containers:
      - name: tomcat
        image: "tomcat:7.0"
        env:
        # 1、stdout为约定关键字，表示采集标准输出日志。
        # 2、配置标准输出日志采集到ES的catalina索引下。
        - name: aliyun_logs_catalina
          value: "stdout"
        # 1、配置采集容器内文件日志，支持通配符。
        # 2、配置该日志采集到ES的access索引下。
        - name: aliyun_logs_access
          value: "/usr/local/tomcat/logs/catalina.*.log"
        # 容器内文件日志路径需要配置emptyDir。
        volumeMounts:
          - name: tomcat-log
            mountPath: /usr/local/tomcat/logs
      volumes:
        - name: tomcat-log
          emptyDir: {}
    ```

    **说明：** 在上面的编排中，通过在Pod中定义环境变量的方式，动态地生成日志采集配置文件，环境变量的具体说明如下：

    -   `aliyun_logs_catalina=stdout`表示要收集容器的stdout日志。
    -   `aliyun_logs_access=/usr/local/tomcat/logs/catalina.*.log`表示要收集容器内/usr/local/tomcat/logs/目录下所有名字匹配catalina.\*.log的文件日志。
    在本方案的Elasticsearch场景下，环境变量中的`$name`表示Index，本例中`$name`即是catalina和access。

7.  在集群管理页左侧导航栏中，选择**工作负载** \> **容器组**。选择部署集群的命名空间**default**，单击**tomcat**名称。

    在容器组 - tomcat页面的**日志**页签下，看到以下内容时，表示Tomcat部署成功。

    ![部署tomcat](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1985659951/p47226.png)

8.  在[阿里云ES控制台](https://elasticsearch.console.aliyun.com/?spm=5176.2020520152.products-grouped.delasticsearch.503816ddrx2e3K)，登录**Kibana控制台**。更多信息，请参见[t615643.dita\#task\_761873](/intl.zh-CN/Elasticsearch/可视化控制/Kibana/登录Kibana控制台.md)。

9.  单击**Management**，然后单击**Index Patterns**，单击**Create index...**，在Create index pattern区域输入access-\*，单击**Next step**。

    ![创建索引](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1985659951/p47235.png)

10. 在**Time Filter field name**下拉框中，选择**@timestamp**，然后单击**Create index pattern**。

11. 如果您需要创建多个索引，按照以下步骤操作。

    1.  单击**Management**，在Kibana区域单击**Index Patterns**。

    2.  单击**Create Index Patterns**，在Index pattern中输入目标索引（索引格式为XXX-\*），单击**Next stop**，然后单击**Create**。


在Kibana控制台左侧导航栏单击**Discover**，您可以看到Tomcat的日志被采集到了指定的ES集群中。

![采集日志到ES](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1985659951/p47233.png)

**说明：** Log-pilot默认采集日志到ES集群时会自动创建格式为index-yyyy.MM.dd的索引。

可以看到通过以上的配置可以方便地将Kubernetes集群中Pod的标准输出日志和容器内文件日志采集到ES集群中。利用开源组件Log-pilot能有效应对分布式Kubernetes集群日志需求，帮助提升运维和运营效率，保障系统持续稳定运行。

