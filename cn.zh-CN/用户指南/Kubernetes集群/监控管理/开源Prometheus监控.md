# 开源Prometheus监控 {#task_1305645 .task}

Prometheus是一款面向云原生应用程序的开源监控工具，本文介绍如何基于阿里云容器Kubernetes版本部署Prometheus监控方案。

-   您已经成功创建一个 Kubernetes 集群，参见[创建Kubernetes集群](intl.zh-CN/用户指南/Kubernetes集群/集群管理/创建Kubernetes集群.md#)。
-   您已连接到Master节点，方便快速查看节点标签等信息，参见[通过 kubectl 连接 Kubernetes 集群](intl.zh-CN/用户指南/Kubernetes集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)。

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

1.  部署Prometheus监控方案。 
    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
    2.  在 Kubernetes 菜单下，在左侧导航栏选择**市场** \> **应用目录**，在右侧选中**ack-prometheus-operator**。
    3.  在应用目录-ack-prometheus-operator页面右侧的**创建**区域，选择目标集群后，单击**创建**，添加 Prometheus-operator。![Prometheus-operator](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24503/156401829552531_zh-CN.png)

 查看部署结果：
        1.  执行以下命令，将集群中的Prometheus 映射到本地9090端口。

            ``` {#codeblock_5zh_ygy_ony}
            kubectl port-forward svc/ack-prometheus-operator-prometheus 9090:9090 -n monitoring
            ```

        2.  在浏览器中访问localhost:9090，即可查看Prometheus。

            **说明：** 默认情况下，无法通过公网访问Prometheus，需要通过本地Proxy的方式查看。

        3.  选择菜单栏**Status**下的**Targets**，查看所有采集任务。

            ![采集任务](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24503/156401829621082_zh-CN.png)

            如果所有任务的状态为**UP**，表示所有采集任务均已正常运行。

            ![Targets](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24503/156401829621084_zh-CN.png)

2.  查看与展示数据聚合。 
    1.  执行以下命令，将集群中的Grafana映射到本地3000端口。 

        ``` {#codeblock_89n_783_ewk}
        kubectl -n monitoring port-forward svc/ack-prometheus-operator-grafana 3000:80
        ```

    2.  在浏览器中访问localhost:3000，选择相应的Dashboard，即可查看相应的聚合内容。 

        **说明：** 默认的用户名/密码是：admin/admin。

        ![Dashboard](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24503/156401829621092_zh-CN.png)

3.  查看告警规则与设置告警压制。 
    -   查看告警规则

        在浏览器中访问`localhost:9090`，选择菜单栏**Alerts**，即可查看当前的告警规则。

        -   红色：正在触发告警。
        -   绿色：正常状态。
        ![Alerts](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24503/156401829621097_zh-CN.png)

    -   设置告警压制

        执行以下命令，并在浏览器中访问`localhost:9093`，选择**Silenced**，设置告警压制。

        ``` {#codeblock_ic8_f0s_7oy}
        kubectl --namespace monitoring port-forward svc/alertmanager-operated 9093
        ```

        ![Silence](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24503/156401829721100_zh-CN.png)


