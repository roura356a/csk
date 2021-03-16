---
keyword: [守护进程集, DaemonSet, K8s]
---

# 创建守护进程集工作负载DaemonSet

DaemonSet保证在每个节点上都运行一个容器副本，常用来部署一些集群的日志、监控或者其他系统管理应用。本文介绍如何在ACK中创建守护进程集工作负载DaemonSet。

## 通过控制台创建DaemonSet

**使用镜像创建DaemonSet**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)[容器服务管理控制台](https://partners-intl.console.aliyun.com/#/cs)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**工作负载** \> **守护进程集**。

5.  在**守护进程集**页面右上角，单击**使用镜像创建**。

6.  设置创建DaemonSet的参数。

    1.  在**应用基本信息**配置向导页面设置应用基本信息。关于参数的详细描述，请参见[使用镜像创建无状态Deployment应用](/intl.zh-CN/Kubernetes集群用户指南/应用/工作负载/创建无状态工作负载Deployment.md)。

    2.  在**容器配置**配置向导页面设置容器信息。关于参数的详细描述，请参见[t17653.md\#section\_ne4\_jlh\_d4r](/intl.zh-CN/Kubernetes集群用户指南/应用/工作负载/创建无状态工作负载Deployment.md)。

    3.  在**高级配置**配置向导页面完成DaemonSet的高级参数的设置。

        DaemonSet会忽略节点的不可调度状态，您可以通过设置节点亲和性、应用亲和性、调度容忍来指定Pod只运行在指定的节点上。关于参数的详细描述，请参见[t17653.md\#section\_49e\_62x\_44j](/intl.zh-CN/Kubernetes集群用户指南/应用/工作负载/创建无状态工作负载Deployment.md)。

7.  单击**创建**。

    成功创建守护进程集后，在守护进程集列表中可以查看新创建的守护进程集。


**使用模板创建DaemonSet**

1.  在**守护进程集**页面右上角，单击**使用模板创建**。

2.  在创建页面，在**模板**区域编辑模板信息。

3.  在**模板**区域下方，单击**创建**。

    成功创建守护进程集后，在守护进程集列表中可以查看新创建的守护进程集。


## 通过kubectl命令行创建DaemonSet

在使用kubectl创建DaemonSet前，您需要下载kubectl并使用kubectl命令连接集群。具体操作，请参见[通过kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。

DaemonSet会忽略节点的无法调度状态，您可以设置以下参数指定Pod运行在指定的节点上：

|参数|描述|
|--|--|
|nodeSelector|只调度到匹配指定标签的节点上。|
|nodeAffinity|设置节点亲和性。根据节点标签将Pod调度到指定节点上，但支持更丰富的节点匹配策略。|
|podAffinity|设置Pod亲和性。根据Pod的标签来选择节点，仅调度到满足条件的Pod所在节点。|

本文以创建一个名为fluentd-elasticsearch的DaemonSet为例，说明如何使用Kubectl创建DaemonSet。

1.  保存以下YAML内容至daemonset.yaml文件中。

    ```
    apiVersion: apps/v1
    kind: DaemonSet
    metadata:
      name: fluentd-elasticsearch
      namespace: kube-system
      labels:
        k8s-app: fluentd-logging
    spec:
      selector:
        matchLabels:
          name: fluentd-elasticsearch
      template:
        metadata:
          labels:
            name: fluentd-elasticsearch
        spec:
          tolerations:
          # this toleration is to have the daemonset runnable on master nodes
          # remove it if your masters can't run pods
          - key: node-role.kubernetes.io/master
            effect: NoSchedule
          containers:
          - name: fluentd-elasticsearch
            image: quay.io/fluentd_elasticsearch/fluentd:v2.5.2
            resources:
              limits:
                memory: 200Mi
              requests:
                cpu: 100m
                memory: 200Mi
            volumeMounts:
            - name: varlog
              mountPath: /var/log
            - name: varlibdockercontainers
              mountPath: /var/lib/docker/containers
              readOnly: true
          terminationGracePeriodSeconds: 30
          volumes:
          - name: varlog
            hostPath:
              path: /var/log
          - name: varlibdockercontainers
            hostPath:
              path: /var/lib/docker/containers
    ```

2.  执行以下命令创建DaemonSet。

    ```
    kubectl create -f daemonset.yaml
    ```

    当输出`daemonset.apps/fluentd-elasticsearch created`时，则说明成功创建守护进程集DaemonSet。


## 相关操作

创建完守护进程集后，您可以进行以下操作：

-   在守护进程集名称右侧的**操作**列中，单击**详情**查看守护进程集的基本信息，包括容器组、访问方式、事件、日志等。
-   在守护进程集名称右侧的**操作**列中，选择**更多** \> **查看Yaml**、**更多** \> **删除**，查看守护进程集的YAML信息和删除守护进程集。

