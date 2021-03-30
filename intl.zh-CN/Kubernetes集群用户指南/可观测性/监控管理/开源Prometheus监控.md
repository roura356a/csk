---
keyword: [开源监控, Prometheus]
---

# 开源Prometheus监控

Prometheus是一款面向云原生应用程序的开源监控工具，本文介绍如何基于阿里云容器Kubernetes版本部署Prometheus监控方案。

-   您已经成功创建一个Kubernetes集群，参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   您已连接到集群，方便快速查看节点标签等信息，参见[通过kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。

对于监控系统而言，监控对象通常分为以下两类：

-   资源监控：节点、应用的资源使用情况，在容器Kubernetes中可理解为节点的资源利用率、集群的资源利用率、Pod的资源利用率等。
-   应用监控：应用内部指标的监控，例如实时统计应用的在线人数，并通过端口暴露来实现应用业务级别的监控与告警等。

在Kubernetes系统中，监控对象具体为：

-   系统组件：Kubernetes集群中内置的组件，包括apiserver、controller-manager、etcd等。
-   静态资源实体：节点的资源状态、内核事件等。
-   动态资源实体：Kubernetes中抽象工作负载的实体，例如Deployment、DaemonSet、Pod等。
-   自定义应用：应用内部需要定制化的监控数据以及监控指标。

对于系统组件和静态资源实体的监控方式，在配置文件中指明即可。

对于动态资源实体的监控，可以使用Prometheus监控部署方案。

## 操作步骤

1.  部署Prometheus监控方案。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在Kubernetes菜单下，在左侧导航栏选择**市场** \> **应用目录**，在右侧选中**ack-prometheus-operator**。

    3.  在应用目录-ack-prometheus-operator页面右侧的**创建**区域，选择目标集群后，单击**创建**，添加Prometheus-operator。

        查看部署结果：

        1.  执行以下命令，将集群中的Prometheus映射到本地9090端口。

            ```
            kubectl port-forward svc/ack-prometheus-operator-prometheus 9090:9090 -n monitoring
            ```

        2.  在浏览器中访问localhost:9090，即可查看Prometheus。
        3.  选择菜单栏**Status** \> **Targets**，查看所有采集任务。

            ![采集任务](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3095659951/p21082.png)

            如果所有任务的状态为**UP**，表示所有采集任务均已正常运行。

            ![Targets](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3095659951/p21084.png)

2.  查看与展示数据聚合。

    1.  执行以下命令，将集群中的Grafana映射到本地3000端口。

        ```
        kubectl -n monitoring port-forward svc/ack-prometheus-operator-grafana 3000:80
        ```

    2.  在浏览器中访问localhost:3000，选择相应的Dashboard，即可查看相应的聚合内容。

        ![Dashboard](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3095659951/p21092.png)

3.  查看告警规则与设置告警压制。

    -   查看告警规则

        在浏览器中访问`localhost:9090`，选择菜单栏**Alerts**，即可查看当前的告警规则。

        -   红色：正在触发告警。
        -   绿色：正常状态。
        ![Alerts](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3095659951/p21097.png)

    -   设置告警压制

        执行以下命令，并在浏览器中访问`localhost:9093`，选择**Silenced**，设置告警压制。

        ```
        kubectl --namespace monitoring port-forward svc/alertmanager-operated 9093
        ```

        ![Silence](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3095659951/p21100.png)


上述步骤为您简单的介绍了如何部署Prometheus监控方案，下面将为您详细介绍一些不同场景下的配置。

## 告警配置

如果您想要设置告警的接收方式以及设置告警接收的模板，请参考如下对alertmanager的config进行配置。

-   设置告警接收方式

    **prometheus-operator**支持钉钉告警和邮件告警，如果需要开启，请参见以下操作进行配置。

    -   配置钉钉告警

        如果需要添加钉钉告警功能，请在应用目录-ack-prometheus-operator页面，单击下方的**参数**页签，找到dingtalk字段将enabled设置为true，Token字段填入钉钉的webhook地址；alertmanager的config字段找到receiver，填写您receivers中对应的钉钉告警名称（默认为Webhook）。

        例如您有两个钉钉机器人，操作示例如下：

        1.  替换钉钉的token配置

            在您的钉钉机器人中，分别拷贝webhook地址替换为dingtalk1和dingtalk2的地址。即，使用webhook地址替换下图中的https://oapi.dingtalk.com/robot/send?access\_token=xxxxxxxxxx。

            ![token配置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3095659951/p56907.png)

        2.  修改receivers

            alertmanager的config字段找到receiver，填写您receivers中对应的钉钉告警名称，本例中分别为webhook1和webhook2。

        3.  修改url的值

            将url中的值替换成实际的dingtalk的值，本例中为dingtalk1和dingtalk2。

            ![webhook配置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3095659951/p56908.png)

        **说明：** 如果需要添加多个钉钉机器人，请将前面的webhook依次递增即可。

    -   配置邮件告警

        如果需要添加邮件告警功能，请在应用目录-ack-prometheus-operator页面，单击下方的**参数**页签，将红色选框内的邮件信息补充完整，在alertmanager的config字段找到receiver，填写您receivers中对应的邮件告警名称（默认为mail）。

        ![邮件告警](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3095659951/p54783.png)

-   设置告警接收模板

    您可以在alertmanager的templateFiles定义我们的告警模板，示例如下。

    ![模板设置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3095659951/p56918.png)


## 存储配置

支持通过TSDB和云盘存储，您可以通过如下操作设置prometheus的数据存放位置。

-   TSDB配置

    如果需要将prometheus的数据存入TSDB，请在应用目录-ack-prometheus-operator页面，单击下方的**参数**页签，找到tsdb字段，将enabled设置为true，并把remoteRead和remoteWrite的url填写完整。

    ![TSDB配置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4095659951/p54784.png)

-   云盘存储配置

    ack-prometheus-operator默认带了阿里云盘存储，可以选择在prometheus或者alertmanager中选择云盘存储，请在应用目录-ack-prometheus-operator页面，单击下方的**参数**页签，找到alertmanager的storage字段或者prometheus的storageSpec字段来配置云盘存储，这里的storageClassName需要填云盘类型，accessModes需要配置访问模式，storage需要填写需要容量。

    ![云盘配置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4095659951/p54800.png)

    **说明：** 本例中，您需要在Prometheus中启用一个SSD云盘，则在Prometheus的storageSpec字段中，将`storageClassName`配置为alicloud-disk-ssd，`accessModes`配置为ReadWriteOnce，`srorage`配置为50Gi，示例如下。

    ![云盘存储](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4095659951/p56923.png)

    配置完成后，您可以在ECS控制台，选择**存储与快照** \> **云盘**，查看您启用的SSD云盘。

    如果您想要复用云盘，请参见[云盘存储卷使用说明](/intl.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/云盘存储卷/云盘存储卷使用说明.md)。


## 通过自定义指标来实现HPA

您可以在prometheus中使用prometheus-adapter自定义指标，通过开启adapter，使得集群中的pod根据指标，自动增加和减少，提升资源利用率。

您可以在应用目录-ack-prometheus-operator页面，单击下方的**参数**页签，找到prometheus-adapter字段，将enabled为true，即可启用adapter。

![启用adapter](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4095659951/p54785.png)

配置完成后，您可以通过执行如下命令，来查看是否正确开启adapter，如果您需要自定义指标，请参见[prometheus-adapter](https://github.com/DirectXMan12/k8s-prometheus-adapter/blob/master/docs/config-walkthrough.md)。

```
kubectl get --raw "/apis/custom.metrics.k8s.io/v1beta1"
```

## Prometheus挂载自定义configmap

以下介绍两种操作，将configmap挂载到pod中的/etc/prometheus/configmaps/路径下。

如果您是第一次部署prometheus-operator，请按照如下进行操作：

您可以在应用目录-ack-prometheus-operator页面，单击下方的**参数**页签，找到prometheus的configmaps字段，填入您自定义configmap名称。

![挂载configmap](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4095659951/p56963.png)

如果集群中已有部署好的prometheus-operator，请按照如下进行操作：

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**应用** \> **Helm**。

5.  在目标Helm应用右侧单击**更新**，进入更新发布页面。

6.  在prometheus和alertmanager的configmaps字段中，填入您自定义configmap名称后，单击**更新**。

    ![更新配置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4095659951/p56964.png)

    **说明：** 例如，您想要定义一个名称为`special-config`的configmap，里面包含prometheus的config文件。如果您想要将在prometheus的pod启动时，将其作为--config.file的参数，那我们就可以在prometheus的configmaps字段添加如下字段，就可以将其挂载到我们的pod中了，其挂载路径为/etc/prometheus/configmaps/。

    special-config的yml定义如下。

    ![special-config](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4095659951/p57825.png)

    prometheus的configmaps字段配置如下。

    ![configmaps](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4095659951/p57828.png)


## Grafana配置

-   Dashboard的外挂配置

    如果您想将Dashboard文件以configmap的方式挂载到Grafana pod中去，您可以在应用目录-ack-prometheus-operator页面，单击下方的**参数**页签，找到`extraConfigmapMounts`，您可以在下图的字段中进行挂载配置。

    ![dashboard外挂配置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4095659951/p57799.png)

    **说明：**

    -   先确保dashboard以confgimap的形式存在于集群中。

        configmap的labels必须要和其他的configmap保持一致。

    -   在Grafana的`extraConfigmapMounts`字段填入我们的configmap信息以及挂载信息。
    -   mountPath是/tmp/dashboards/。
    -   configmap是您定义的configmap的名称。
    -   name是您Dashboard的json名称。
-   Dashboard的持久化

    Grafana支持Dashboard的持久化操作如下：

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
    2.  在控制台左侧导航栏中，单击**集群**。
    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**应用管理**。
    4.  在左侧导航栏，选择**应用** \> **Helm**。
    5.  找到ack-prometheus-operator，然后单击右侧**更新**，弹出**更新发布**对话框。
    6.  在grafana的字段下找到persistence选项，按照下图配置完成Grafana的持久化操作。
    ![grafana的持久化操作](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4095659951/p103389.png)

    如果我们需要导出我们的Dashboard到本地，我们可以将需要保存的Dashboard通过导出为JSON文件的方式，将其保存到本地。请参见[Grafana导出](https://grafana.com/docs/reference/export_import/#export-and-import)。


## 常见问题

-   钉钉配置后，没有收到告警。
    1.  获取钉钉的webhook地址。请参见[场景三：使用钉钉实现Kubernetes监控告警](/intl.zh-CN/Kubernetes集群用户指南/可观测性/监控管理/事件监控.md)。
    2.  找到dingtalk字段，将enabled设置为true，将Token字段填入钉钉的webhook地址。请参见[告警配置](#section_6r6_hsk_wc0)中的**钉钉告警配置**。
-   部署prometheus-operator时报错。

    报错信息如下

    ```
    Can't install release with errors: rpc error: code = Unknown desc = object is being deleted: customresourcedefinitions.apiextensions.k8s.io "xxxxxxxx.monitoring.coreos.com" already exists
    ```

    在卸载prometheus-operator的时候没有将上一次部署的自定义资源（crd）及时清理掉，执行如下命令，删除crd并重新部署。

    ```
    kubectl delete crd prometheuses.monitoring.coreos.com
    kubectl delete crd prometheusrules.monitoring.coreos.com
    kubectl delete crd servicemonitors.monitoring.coreos.com
    kubectl delete crd alertmanagers.monitoring.coreos.com
    ```

-   邮件告警没有生效 。

    邮件告警没有生效，有可能是因为`smtp_auth_password`填写的是您的登录密码，而非授权码。另外stmp的服务器地址需要加端口号。

-   如果在单击YAML更新时，出现当前集群暂时无法访问，请稍后重试或提交工单反馈信息。

    此问题原因是tiller的配置文件过大，导致的集群无法访问，您可以先将部分注释删除，再将配置文件以configmap形式，挂载到pod中，目前prometheus-operator只支持prometheus和alertmanager pod的挂载，详情请参见[Prometheus挂载自定义configmap](#section_20m_m2b_ki9)中的方法二。

-   部署prometheus-operator后，如何开启里面的功能？

    当部署好prometheus-operator后，如果有开启部分功能，在集群信息页面，选择**应用** \> **Helm**，在**ack-prometheus-operator**右侧，单击**更新**，找到对应的开关，做相应的设置，然后单击**更新**开启您想要的功能。

-   TSDB和阿里云云盘的选择。

    TSDB支持的地域比较少，而阿里云云盘是全域支持，数据回收策略请参见以下配置。

    ![数据回收策略](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5095659951/p57817.png)

-   Grafana dashboard显示有问题

    在集群信息页面选择选择**应用** \> **Helm**，在**ack-prometheus-operator**右侧，单击**更新**，查看**clusterVersion**的值是否为正确的集群版本。Kubernetes集群是1.16以前的版本，这里请填写1.14.8-aliyun.1，1.16及以后的版本，请填写1.16.6-aliyun.1。

-   删除ack-prometheus的命名空间后，重新安装ack-prometheus失败。

    只删除ack-prometheus的命名空间，会导致资源删除后有残留配置，影响再次安装。您可以执行以下操作，删除残余配置。

    1.  删除RBAC权限。
        1.  删除ClusterRole。

            ```
            kubectl delete ClusterRole ack-prometheus-operator-grafana-clusterrole
            kubectl delete ClusterRole ack-prometheus-operator-kube-state-metrics
            kubectl delete ClusterRole psp-ack-prometheus-operator-kube-state-metrics
            kubectl delete ClusterRole psp-ack-prometheus-operator-prometheus-node-exporter
            kubectl delete ClusterRole ack-prometheus-operator-operator
            kubectl delete ClusterRole ack-prometheus-operator-operator-psp
            kubectl delete ClusterRole ack-prometheus-operator-prometheus
            kubectl delete ClusterRole ack-prometheus-operator-prometheus-psp
            ```

        2.  删除ClusterRoleBinding。

            ```
            kubectl delete ClusterRoleBinding ack-prometheus-operator-grafana-clusterrolebinding
            kubectl delete ClusterRoleBinding ack-prometheus-operator-kube-state-metrics
            kubectl delete ClusterRoleBinding psp-ack-prometheus-operator-kube-state-metrics
            kubectl delete ClusterRoleBinding psp-ack-prometheus-operator-prometheus-node-exporter
            kubectl delete ClusterRoleBinding ack-prometheus-operator-operator
            kubectl delete ClusterRoleBinding ack-prometheus-operator-operator-psp
            kubectl delete ClusterRoleBinding ack-prometheus-operator-prometheus
            kubectl delete ClusterRoleBinding ack-prometheus-operator-prometheus-psp
            ```

    2.  删除CRD。

        ```
        kubectl delete crd alertmanagerconfigs.monitoring.coreos.com
        kubectl delete crd alertmanagers.monitoring.coreos.com
        kubectl delete crd podmonitors.monitoring.coreos.com
        kubectl delete crd probes.monitoring.coreos.com
        kubectl delete crd prometheuses.monitoring.coreos.com
        kubectl delete crd prometheusrules.monitoring.coreos.com
        kubectl delete crd servicemonitors.monitoring.coreos.com
        kubectl delete crd thanosrulers.monitoring.coreos.com
        ```


