# 部署Prometheus监控方案 {#concept_nzh_x4x_4fb .concept}

Prometheus是一款面向云原生应用程序的开源监控工具，本文介绍如何基于阿里云容器Kubernetes版本部署Prometheus监控方案。

## 背景信息 {#section_ifs_pty_pfb .section}

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

## 前提条件 {#section_csx_4ty_pfb .section}

-   您已经成功创建一个 Kubernetes 集群，参见[创建Kubernetes集群](intl.zh-CN/用户指南/Kubernetes 集群/集群管理/创建Kubernetes集群.md#)。
-   您已连接到Master节点，方便快速查看节点标签等信息，参见[通过 kubectl 连接 Kubernetes 集群](intl.zh-CN/用户指南/Kubernetes 集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)。

## 部署Prometheus监控方案 {#section_jtv_wty_pfb .section}

1.  执行以下命令，下载prometheus-operator代码。

    ```
    git clone https://github.com/AliyunContainerService/prometheus-operator 
    ```

2.  执行以下命令，部署Prometheus监控方案。

    ```
    cd prometheus-operator/contrib/kube-prometheus
    kubectl apply -f manifests
    ```

3.  执行以下命令，设置Prometheus访问。

    ```
    kubectl --namespace monitoring port-forward svc/prometheus-k8s 9090
    ```

4.  查看部署结果：
    1.  在浏览器中访问`localhost:9090`，即可查看Prometheus。

        **说明：** 默认情况下，无法通过公网访问Prometheus，需要通过本地Proxy的方式查看。

    2.  选择菜单栏**Status**下的**Targets**，查看所有采集任务。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24503/154174943821082_zh-CN.png)

        如果所有任务的状态为**UP**，表明所有采集任务均已正常运行。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24503/154174943821084_zh-CN.png)


## 查看与展示数据聚合 {#section_ljp_yty_pfb .section}

1.  执行以下命令，访问Grafana：

    ```
    kubectl --namespace monitoring port-forward svc/grafana 3000
    ```

2.  在浏览器中访问`localhost:3000`，选择相应的Dashboard，即可查看相应的聚合内容。

    **说明：** 默认的用户名/密码是：admin/admin。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24503/154174943821092_zh-CN.png)


## 查看告警规则与设置告警压制 {#section_ygc_15y_pfb .section}

-   **查看告警规则**

    在浏览器中访问`localhost:9090`，选择菜单栏**Alerts**，即可查看当前的告警规则。

    -   红色：正在触发告警。
    -   绿色：正常状态。
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24503/154174943921097_zh-CN.png)

-   **设置告警压制**

    执行以下命令，并在浏览器中访问`localhost:9093`，选择**Silenced**，设置告警压制。

    ```
    kubectl --namespace monitoring port-forward svc/alertmanager-main 9093
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24503/154174943921100_zh-CN.png)


