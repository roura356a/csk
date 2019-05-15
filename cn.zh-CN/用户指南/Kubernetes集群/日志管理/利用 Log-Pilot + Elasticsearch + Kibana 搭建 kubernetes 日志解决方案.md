# 利用 Log-Pilot + Elasticsearch + Kibana 搭建 kubernetes 日志解决方案 {#concept_bmg_fkb_wdb .concept}

开发者在面对 Kubernetes 分布式集群下的日志需求时，常常会感到头疼，既有容器自身特性的原因，也有现有日志采集工具的桎梏，主要包括：

-   容器本身特性：
    -   采集目标多：容器本身的特性导致采集目标多，需要采集容器内日志、容器 stdout。对于容器内部的文件日志采集，现在并没有一个很好的工具能够去动态发现采集。针对每种数据源都有对应的采集软件，但缺乏一站式的工具。
    -   弹性伸缩难：Kubernetes 是分布式的集群，服务、环境的弹性伸缩对于日志采集带来了很大的困难，无法像传统虚拟机环境下那样，事先配置好日志的采集路径等信息，采集的动态性以及数据完整性是非常大的挑战。
-   现有日志工具的一些缺陷：
    -   缺乏动态配置的能力。目前的采集工具都需要事先手动配置好日志采集方式和路径等信息，因为它无法能够自动感知到容器的生命周期变化或者动态漂移，所以它无法动态地去配置。
    -   日志采集重复或丢失的问题。因为现在的一些采集工具基本上是通过 tail 的方式来进行日志采集的，那么这里就可能存在两个方面的问题：一个是可能导致日志丢失，比如采集工具在重启的过程中，而应用依然在写日志，那么就有可能导致这个窗口期的日志丢失；而对于这种情况一般保守的做法就是，默认往前多采集 1M 日志或 2M 的日志，那么这就又会可能引起日志采集重复的问题。
    -   未明确标记日志源。因为一个应用可能有很多个容器，输出的应用日志也是一样的，那么当我们将所有应用日志收集到统一日志存储后端时，在搜索日志的时候，我们就无法明确这条日志具体是哪一个节点上的哪一个应用容器产生的。

本文档将介绍一种 Docker 日志收集工具 Log-Pilot，结合 Elasticsearch 和 Kibana 等工具，形成一套适用于 Kubernetes 环境下的一站式日志解决方案。

## Log-Pilot 介绍 {#section_eys_gkb_wdb .section}

log-Pilot 是一个智能容器日志采集工具，它不仅能够高效便捷地将容器日志采集输出到多种存储日志后端，同时还能够动态地发现和采集容器内部的日志文件。

针对前面提出的日志采集难题，Log-Pilot 通过声明式配置实现强大的容器事件管理，可同时获取容器标准输出和内部文件日志，解决了动态伸缩问题，此外，Log-Pilot 具有自动发现机制，CheckPoint 及句柄保持的机制，自动日志数据打标，有效应对动态配置、日志重复和丢失以及日志源标记等问题。

目前 log-pilot 在 Github 完全开源，项目地址是 [https://github.com/AliyunContainerService/log-pilot](https://github.com/AliyunContainerService/log-pilot) 。您可以深入了解更多实现原理。

## 针对容器日志的声明式配置 {#section_fys_gkb_wdb .section}

Log-Pilot 支持容器事件管理，它能够动态地监听容器的事件变化，然后依据容器的标签来进行解析，生成日志采集配置文件，然后交由采集插件来进行日志采集。

在 Kubernetes 下，Log-Pilot 可以依据环境变量 `aliyun_logs_$name = $path` 动态地生成日志采集配置文件，其中包含两个变量：

-   $name 是我们自定义的一个字符串，它在不同的场景下指代不同的含义，在本场景中，将日志采集到 ElasticSearch 的时候，这个 $name 表示的是 Index。
-   另一个是 $path，支持两种输入形式，stdout 和容器内部日志文件的路径，对应日志标准输出和容器内的日志文件。
    -   第一种约定关键字 stdout 表示的是采集容器的标准输出日志，如本例中我们要采集 tomcat 容器日志，那么我们通过配置标签 `aliyun.logs.catalina=stdout` 来采集 tomcat 标准输出日志。
    -   第二种是容器内部日志文件的路径，也支持通配符的方式，通过配置环境变量 `aliyun_logs_access=/usr/local/tomcat/logs/*.log`来采集 tomcat 容器内部的日志。当然如果你不想使用 aliyun 这个关键字，Log-Pilot 也提供了环境变量 PILOT\_LOG\_PREFIX 可以指定自己的声明式日志配置前缀，比如 `PILOT_LOG_PREFIX: "aliyun,custom"`。

此外，Log-Pilot 还支持多种日志解析格式，通过 `aliyun_logs_$name_format=<format>` 标签就可以告诉 Log-Pilot 在采集日志的时候，同时以什么样的格式来解析日志记录，支持的格式包括：none、json、csv、nginx、apache2 和 regxp。

Log-Pilot 同时支持自定义 tag，我们可以在环境变量里配置 `aliyun_logs_$name_tags="K1=V1,K2=V2"`，那么在采集日志的时候也会将 K1=V1 和 K2=V2 采集到容器的日志输出中。自定义 tag 可帮助您给日志产生的环境打上 tag，方便进行日志统计、日志路由和日志过滤。

## 日志采集模式 {#section_iys_gkb_wdb .section}

本文档采用 node 方式进行部署，通过在每台机器上部署一个 Log-Pilot 实例，收集机器上所有 Docker 应用日志。

该方案跟在每个 Pod 中都部署一个 logging 容器的模式相比，最明显的优势就是占用资源较少，在集群规模比较大的情况下表现出的优势越明显，这也是社区推荐的一种模式。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16698/155788410810636_zh-CN.png)

## 前提条件 {#section_kys_gkb_wdb .section}

您已经开通容器服务，并创建了一个 Kubernetes 集群。本示例中，创建的 Kubernetes 集群位于华东 1 地域。

请务必确保到 ElasticSearch 集群网络可达。

## 步骤1 部署ElasticSearch集群 {#section_xml_n11_12b .section}

请参考[购买ES产品](../../../../intl.zh-CN/快速入门/购买和配置.md#section_bpg_tjl_zgb)完成ElasticSearch集群的部署，完成后，进行下一步操作。

**说明：** 默认情况下，阿里云ElasticSearch集群（简称ES集群）当新增的文档发现没有索引时，不允许自动创建索引，而Log-Pilot在自动采集容器日志时需要依据日志采集配置来自动创建文档索引，因此我们这里需要开启**自动创建索引**功能。

您可通过如下操作，开启**自动创建索引**功能。

1.  单击目标集群右侧的**管理**，进入基本信息页面。
2.  选择**ES集群配置**，在YML文件配置区域框右侧单击**修改配置**，弹出YMAL参数配置页面。
3.  自动创建索引选择允许自动创建索引，勾选**该操作会重启实例，请确认后操作。**后，单击**确认**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16698/155788410845076_zh-CN.png)


## 步骤2 部署 log-pilot组件 {#section_fgg_511_12b .section}

为降低节点的资源消耗，Log-Pilot 组件采用的是 DaemonSet 方式部署到每个集群的 Node 节点上，部署 Log-Pilot 日志采集工具，操作如下：

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**应用** \> **无状态**，进入无状态页面。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/129974/155788410839478_zh-CN.png)

3.  单击右上角的**使用模板创建**，进入使用模板创建页面。
4.  选择需要部署 log-pilot 组件的**集群**，并选择**kube-system**作为**命名空间**。
5.  **示例模板**选择**自定义**，并将以下内容复制到**模板**中，单击**创建**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16698/155788410845109_zh-CN.png)

    ```
    apiVersion: extensions/v1beta1
    kind: DaemonSet
    metadata:
    name: log-pilot
    labels:
    app: log-pilot
    # 设置期望部署的namespace
    namespace: kube-system
    spec:
    updateStrategy:
    type: RollingUpdate
    template:
    metadata:
    labels:
    app: log-pilot
    annotations:
    scheduler.alpha.kubernetes.io/critical-pod: ''
    spec:
    # 是否允许部署到Master节点上
    tolerations:
    - key: node-role.kubernetes.io/master
    effect: NoSchedule
    containers:
    - name: log-pilot
    # 版本请参考https://github.com/AliyunContainerService/log-pilot/releases
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
    # 请确保集群到ES网络可达
    - name: "ELASTICSEARCH_HOSTS"
    value: "{es_endpoint}:{es_port}"
    # 配置ES访问权限
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

    **说明：** 参数说明：

    -   `{es_endpoint}`：ES 集群的访问地址。
        -   如果 Kubernetes 集群和部署的 ES 集群在同一个 VPC 下，则该地址为 ES 集群基本信息中的**内网地址**。
        -   如果 Kubernetes 集群和部署的 ES 集群在不同一个 VPC 下，则该地址为 ES 集群基本信息中的**公网地址**。
    -   `{es_port}`：ES 集群的访问端口，一般是9200。
    -   `{es_username}`：访问 ES 集群的用户名。默认用户名 elastic。
    -   `{es_password}`：访问 ES 集群的用户密码。即为创建 ES 集群时，设置的用户密码。
6.  单击**Kubernetes 控制台**前往控制台查看创建进度。
7.  选择命名空间为**kube-system**，在**负载均衡** \> **容器组**下，看到如下图所示时，表示运行正常。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16698/155788410845111_zh-CN.png)

    **说明：** 您也可以[通过 kubectl 连接 Kubernetes 集群](intl.zh-CN/用户指南/Kubernetes集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)，执行如下命令来查看 Log-Pilot 组件的运行状态。

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


## 步骤3 采集日志 {#section_imr_20s_n2r .section}

这里以部署一个Tomcat为例，通过创建 Deploment 应用配置日志采集（配置方式同样适用 StatefulSet），来测试日志是否能正常采集、索引和显示。

1.  在 Kubernetes 菜单下，单击左侧导航栏中的**应用** \> **无状态**，进入无状态页面。
2.  单击右上角的**使用模板创建**，进入使用模板创建页面。
3.  选择**集群**为**k8s-cluster**（必须和Log-Pilot 组件部署在同一集群下），并选择目标**命名空间**。
4.  **示例模板**选择**自定义**，并将以下内容复制到**模板**中，单击**创建**。

    编排模板如下：

    ``` {#codeblock_rx7_8yd_xyd}
    apiVersion: v1
    kind: Pod
    metadata:
    name: tomcat
    spec:
    containers:
    - name: tomcat
    image: "tomcat:7.0"
    env:
    # 1、stdout为约定关键字，表示采集标准输出日志
    # 2、配置标准输出日志采集到ES的catalina索引下
    - name: aliyun_logs_catalina
    value: "stdout"
    # 1、配置采集容器内文件日志，支持通配符
    # 2、配置该日志采集到ES的access索引下
    - name: aliyun_logs_access
    value: "/usr/local/tomcat/logs/catalina.*.log"
    # 容器内文件日志路径需要配置emptyDir
    volumeMounts:
    - name: tomcat-log
    mountPath: /usr/local/tomcat/logs
    volumes:
    - name: tomcat-log
    emptyDir: {}
    ```

    **说明：** 在上面的编排中，通过在 Pod 中定义环境变量的方式，动态地生成日志采集配置文件，环境变量的具体说明如下：

    -   `aliyun_logs_catalina=stdout`表示要收集容器的 stdout 日志。
    -   `aliyun_logs_access=/usr/local/tomcat/logs/catalina.*.log` 表示要收集容器内 /usr/local/tomcat/logs/ 目录下所有名字匹配 catalina.\*.log 的文件日志。
    在本方案的 Elasticsearch 场景下，环境变量中的 `$name` 表示 Index，本例中 `$name`即是 catalina 和 access 。

5.  选择**应用** \> **容器组**，选择部署 Tomcat 的集群**cluster-k8s**和命名空间**default**，在目标 Tomcat 右侧选择**更多** \> **日志**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16698/155788410847225_zh-CN.png)

    在容器组 - tomcat页面的**日志**页签下，看到如下内容时，表示 Tomcat 部署成功。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16698/155788410847226_zh-CN.png)

6.  在阿里云 ES 控制台，单击**Kibana控制台**，登录 Kibana。
7.  单击**Management**，在Configure an index pattern区域输入access-\*，单击**Create**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16698/155788410847235_zh-CN.png)

8.  **（可选）**：如果您需要创建多个索引，按照如下步骤操作。
    1.  单击**Management**，在Kibana区域单击**Index Patterns**。
    2.  单击**Create Index Patterns**，在Index name or pattern中输入目标索引（索引格式为XXX-\*），单击**Create**。
9.  单击**Discover**，您可以看到 Tomcat 的日志被采集到了指定的 ES 集群中。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16698/155788410847233_zh-CN.png)

    **说明：** Log-Pilot 默认采集日志到 ES 集群时会自动创建格式为 index-yyyy.MM.dd 的索引。


我们可以看到只需要通过上面简单的配置就可以很方便地将 Kubernetes 集群中Pod的标准输出日志和容器内文件日志采集到 ES 集群中。通过这个方案，我们能有效应对分布式 Kubernetes 集群日志需求，可以帮助提升运维和运营效率，保障系统持续稳定运行。

