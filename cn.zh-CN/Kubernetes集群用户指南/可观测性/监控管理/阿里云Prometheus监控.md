---
keyword: [ACK接入Prometheus, Prometheus监控报警, Prometheus监控指标, Grafana展示监控指标]
---

# 阿里云Prometheus监控

您可以通过阿里云Prometheus监控查看容器服务Kubernetes版ACK（Container Service for Kubernetes）预先配置的监控大盘和监控性能指标。本文为您介绍如何在ACK中接入阿里云的Prometheus监控，如何配置Prometheus监控报警及如何自定义Prometheus监控指标并通过Grafana展示。

阿里云Prometheus监控全面对接开源Prometheus生态，支持类型丰富的组件监控，提供多种开箱即用的预置监控大盘，且提供全面托管的Prometheus服务。借助阿里云Prometheus监控，您无需自行搭建Prometheus监控系统，因而无需关心底层数据存储、数据展示、系统运维等问题。

整体而言，与开源Prometheus监控相比，阿里云Prometheus监控的优势体现为：

-   更轻量、更稳定、更准确的重试机制
    -   与开源Prometheus监控相比，阿里云Prometheus监控的整体结构更加轻量化。您无需自行搭建Prometheus监控系统，仅需安装阿里云Prometheus监控探针PromAgent即可开始监控业务。

        ![dg_arms_prometheus_advantage](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7095659951/p76745.png)

    -   在系统稳定性方面，开源Prometheus监控一般会占用16GB~128GB的内存，而阿里云Prometheus监控仅占用200MB~1GB的内存和1核CPU。相比开源Prometheus监控，阿里云Prometheus监控更加稳定。
    -   在抓取和写入数据的准确性方面，开源Prometheus监控仅抓取1次数据，并且瞬时写入存储组件时存在丢弃逻辑。而阿里云Prometheus监控抓取数据会重试多次，持续并发写入存储组件，不存在丢弃逻辑。
-   数据量无上限
    -   开源Prometheus监控的数据采集能力上限为百万条Metrics级别，而阿里云Prometheus监控的数据采集能力可以按照K8s副本数水平扩展，从而均衡分解采集任务。
    -   开源Prometheus监控的数据存储能力上限受本地磁盘大小的限制，而阿里云Prometheus监控使用中心云存储服务，理论上存储能力无上限。
-   完全兼容开源生态
    -   阿里云Prometheus监控完全兼容Prometheus监控开源生态链路中的客户端和查询语言部分，兼容并优化开源生态链路中的采集规则和使用价值部分。

        ![dg_arms_prometheus_ecology](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7095659951/p76971.png)

    -   阿里云Prometheus监控兼容并提供三种主流采集规则的实现，包括标准开源prometheus.yaml采集规则配置文件、适合自定义K8s内监控的采集规则ServiceMonitor、以及默认采集规则Annotation。与开源Prometheus监控相比，阿里云Prometheus监控无需重启，即可使用prometheus.yaml配置文件动态更新采集规则。在Deployment文件里也无需编写多行代码，仅需增加以下3个Annotation注解即可。

        ```
        prometheus.io/scrape: "true"
        prometheus.io/port: "9090"
        prometheus.io/path: "/metrics"
        ```

    -   阿里云Prometheus监控兼容可视化Grafana。通过配置Prometheus HTTP API URL即可在Grafana中完成数据源的多租户隔离，以及Grafana大盘的多租户隔离。阿里云Prometheus监控还兼容Grafana的Explore数据调试模块。
    -   阿里云Prometheus监控兼容开源Prometheus监控的HTTP API模块，完整支持query、query\_range和labelValues 3个标准数据查询接口，并通过在数据URL中添加/userId/clusterId/regionId/这组ID达到多租户隔离的效果。
    -   阿里云Prometheus监控虽然使用ARMS已有的告警系统，但完全兼容开源Prometheus监控的告警规则PromQL。
-   节省成本
    -   阿里云Prometheus监控支持默认K8s监控。在您安装默认K8s监控后，阿里云Prometheus监控会自动为您创建默认的Exporter、采集规则、Grafana大盘以及ARMS告警。您的时间成本可由原来使用开源Prometheus监控K8s的3天左右降低至10分钟左右。

        ![监控大盘1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7095659951/p103080.png)

    -   阿里云Prometheus监控支持开源组件监控。您仅需输入阿里云账号的AccessKey ID和AccessKey Secret，以及RDS和Redis组件的账号和密码，阿里云Prometheus监控即可为您默认生成这些组件的Exporter，并为您创建默认的组件大盘。您的时间成本可由原来使用开源Prometheus监控开源组件的7天左右降低至3分钟左右。

        ![监控大盘2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7095659951/p103081.png)

    -   阿里云Prometheus监控支持一键安装、一键卸载，以及通过健康检查功能调试Prometheus监控。您的时间成本可由原来使用开源Prometheus监控的1天左右降低至3分钟左右。

有关阿里云Prometheus监控的更多信息，请参见[阿里云Prometheus监控概述]()。

## 开启阿里云Prometheus监控

您可以通过以下三种方式开启阿里云Prometheus监控：

**通过配置集群参数开启Prometheus监控。**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击页面右上角的**创建集群**。

4.  选择目标集群模板，然后配置创建集群参数。在**组件配置**页面，选中**使用Prometheus监控服务**。

    ![组件配置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5816545161/p248500.png)

    有关创建集群的具体步骤，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。

    **说明：** 在创建集群时，系统默认选中**使用Prometheus监控服务**。

    集群创建完成后，系统将自动配置阿里云Prometheus监控服务。


**通过ACK控制台Prometheus监控开启Prometheus监控。**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理左侧导航栏中，选择**运维管理** \> **Prometheus监控**。

5.  在**Prometheus监控**页面中间，单击**开始安装**。


**通过ACK控制台应用目录开启阿里云Prometheus监控。**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

3.  在应用目录页面的**阿里云应用**页签中，搜索并单击**ack-arms-prometheus**。

4.  在应用目录 - ack-arms-prometheus右侧的**创建**区域中，选择目标集群，然后单击**创建**。

    **说明：** **命名空间**和**发布名称**默认为**arms-prom**。


**执行结果**

安装完成后会自动跳转到应用arms-prom页面，可查看应用信息。

![arm-prom](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5816545161/p248613.png)

## 查看阿里云Prometheus Grafana大盘

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**运维管理** \> **Prometheus监控**。

5.  在**Promethues监控**页面，单击需要查看的Grafana监控大盘名，即可查看相应的监控数据。


## 配置Prometheus监控报警

为监控任务创建报警可在满足报警条件时通过邮件、短信、钉钉等渠道实时报警，主动帮助您发现异常。报警规则被触发时会向您指定的联系人分组发送通知，而在创建联系人分组之前必须先创建联系人。创建联系人时，您可以指定联系人用于接收通知的手机号码和邮箱地址，也可以提供用于自动发送报警通知的钉钉机器人地址。

**说明：** 如需将钉钉机器人添加为联系人，则需要先获取钉钉机器人的地址。具体操作，请参见[t152339.dita\#concept\_106247\_zh](/cn.zh-CN/大盘和报警/设置钉钉机器人报警.md)。

1.  登录[ARMS控制台](https://arms.console.aliyun.com/#/home)。

2.  在控制台左侧导航栏，选择**报警管理** \> **联系人管理**。

3.  在**联系人**页签，单击右上角的**新建联系人**。配置联系人信息，然后单击**确认**。

    ![新建联系人](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6096674161/p245266.png)

4.  配置报警规则。

    1.  登录[ARMS控制台](https://arms.console.aliyun.com/#/home)。

    2.  在控制台左侧导航栏单击**Prometheus监控**。

    3.  在Prometheus监控页面单击**K8s**列目标集群名称。

    4.  在左侧导航栏单击**报警配置**。

    5.  在报警规则说明列表单击对应报警**操作**列的**编辑**，输入PromQL语句配置报警规则，配置完成后单击**确定**。或者单击**开启**可直接开启预置的报警规则。

        关于PromQL语句的配置，请参见[快速创建ARMS报警](/cn.zh-CN/快速入门/快速创建ARMS报警.md)。

    **说明：** 报警规则也可在[ARMS控制台](https://arms.console.aliyun.com/#/home)的**报警管理** \> **报警策略管理** 进行管理。

    **验证结果**

    进行手动测试，触发钉钉告警。正常触发告警如下：

    ![监控告警](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0022285161/p249833.png)


## 自定义Prometheus监控指标并通过Grafana展示

**方式一：通过Annotations自定义指标监控**

通过给Deployment Pod Template加入Annotations的方式自定义监控，阿里云ARMS应用监控组件将会使ARMS Prometheus监控自动获取Pod自定义指标。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

5.  在无状态页面中，创建应用。

    1.  单击**使用镜像创建**。

    2.  在配置向导**应用基本信息**中填写应用的基本信息，单击**下一步**。

    3.  创建一个Web应用，暴露5000端口。

        以镜像`yejianhonghong/pindex`为示例。

        ![容器配置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7063915161/p245358.png)

    4.  单击**下一步**。

    5.  给Pod添加ARMS相关的Annotations。

        prometheus.io/port表示Prometheus要scrape的endpoint端口，prometheus.io/path表示Prometheus要scrape的endpoint路径。

        ![标签和注解](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9068535161/p245359.png)

    6.  单击**创建**，完成应用创建任务。

6.  在服务页面创建服务。

    1.  在集群管理页左侧导航栏中，选择**服务与路由** \> **服务**。

    2.  单击服务页面右上角的**创建**。

    3.  在**类型**中选择**负载均衡**型服务及**公网访问**。

    4.  在**关联**中选择[步骤4](#step_fez_f3e_d8s)创建的应用。

    5.  单击**创建**，完成服务创建任务。

    具体操作，请参见[创建服务](/cn.zh-CN/Kubernetes集群用户指南/网络/Service管理/管理服务.md)。

7.  配置自定义指标。

    1.  登录[ARMS控制台](https://arms.console.aliyun.com/#/home)。

    2.  在控制台左侧导航栏单击**Prometheus监控**。

    3.  在Prometheus监控页面，单击**K8s**列目标集群名称。

    4.  在左侧导航栏单击**设置**， 然后在右侧区域单击**Targets\(beta\)**页签，可以看到自定义的指标已经配置成功。

        ![自定义指标](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9068535161/p245360.png)

8.  访问[步骤5](#step_x73_rdc_yg6)所创建服务暴露出来的公网IP，增加指标值。

    具体指标的配置，请参见[DATA MODEL](https://prometheus.io/docs/concepts/data_model/)。

    ![增加指标值](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9068535161/p245361.png)

9.  单击Prometheus任意一个预置的图表进入Grafana，然后单击**添加Panal** ，选择**Graph**类型，将**Metrics**填写为current\_person\_counts。

10. 保存配置即可观察到自定义指标的Grafana图形。

    ![Grafana](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7063915161/p245363.png)


**方式二：通过ServiceMonitor自定义指标监控**

通过ServiceMonitor方式自定义指标监控在部署的时候不需要配置Annotations，但是要给Service对象添加标签。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在无状态页面中，创建应用。

    1.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

    2.  单击**使用镜像创建**。

    3.  在配置向导**应用基本信息**中填写应用的基本信息，单击**下一步**。

    4.  创建一个Web应用，暴露5000端口。

        以镜像`yejianhonghong/pindex`为示例。

        ![容器配置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7063915161/p245358.png)

        单击**下一步**。

    5.  单击**创建**，完成应用创建任务。

5.  在服务页面创建服务。

    1.  在集群管理页左侧导航栏中，选择**服务与路由** \> **服务**。

    2.  单击服务页面右上角的**创建**。

    3.  在**类型**中选择**负载均衡**型服务及**公网访问**。

    4.  在**关联**中选择[步骤4](#step_fez_f3e_d8s)创建的应用。

    5.  添加标签。

        该标签将被用于serviceMonitor的selector。

        ![创建服务](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5684815161/p246409.png)

    6.  单击**创建**，完成服务创建任务。

    具体操作，请参见[创建服务](/cn.zh-CN/Kubernetes集群用户指南/网络/Service管理/管理服务.md)。

6.  使Prometheus获得服务Scape Endpioint。

    1.  登录[Prometheus控制台](https://prometheus.console.aliyun.com/#/home)。

    2.  在控制台左侧导航栏选择**监控列表**，单击**K8s**列目标集群名称。

    3.  在左侧导航栏单击**设置**， 然后在右侧区域单击**服务发现**页签。

    4.  在ServiceMonitor页签右上角单击**添加ServiceMonitor**创建ServiceMonitor。

        模板文件如下所示。

        ```
        apiVersion: monitoring.coreos.com/v1
        kind: ServiceMonitor
        metadata:
          #填写一个唯一名称。
          name: custom-metrics-pindex
          #填写目标命名空间。
          namespace: default
        spec:
          endpoints:
          - interval: 30s
            #填写service.yaml中对应的Port的Name字段的值（参考上图）。
            port: web
            #填写Service对应的Path的值。
            path: /access
          namespaceSelector:
            any: true
            #Nginx Demo的命名空间。
          selector:
            matchLabels:
              #填写service.yaml的Label字段的值以定位目标service.yaml。
              app: custom-metrics-pindex
        ```

        单击**确定**完成ServiceMonitor创建。

    5.  在**Targets\(beta\)**页签，可见Prometheus已经获得服务Scape Endpioint。

        ![Scape Endpioint](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7063915161/p246444.png)

        **说明：** 这里ServiceMonitor定义比Annotations方式更直观，指示出了Namespace和Service名称。

7.  访问[步骤5](#step_v29_tra_ics)创建的服务暴露出来的公网IP，增加指标值。

    关于指标的配置，请参见[DATA MODEL](https://prometheus.io/docs/concepts/data_model/)。

    ![增加指标值](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9068535161/p245361.png)

8.  单击Prometheus任意一个预置的图表进入Grafana，然后单击**添加Panal** ，选择**Graph**类型，将**Metrics**填写为current\_person\_counts。

9.  保存配置即可观察到自定义指标的Grafana图形。

    ![Grafana](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7063915161/p245363.png)


## 常见问题

删除阿里云Prometheus的命名空间后，重新安装阿里云Prometheus失败。

只删除阿里云Prometheus的命名空间，会导致资源删除后有残留配置，影响再次安装。您可以执行以下操作，删除残余配置。

1.  删除ClusterRole。

    ```
    kubectl delete ClusterRole arms-kube-state-metricskubectl delete ClusterRole arms-node-exporter
    kubectl delete ClusterRole arms-prom-ack-arms-prometheus-role
    kubectl delete ClusterRole arms-prometheus-oper3
    ```

2.  删除ClusterRoleBinding。

    ```
    kubectl delete ClusterRoleBinding arms-node-exporter
    kubectl delete ClusterRoleBinding arms-prom-ack-arms-prometheus-role-binding
    kubectl delete ClusterRoleBinding arms-prometheus-oper-bind2
    kubectl delete ClusterRoleBinding kube-state-metrics
    ```


**相关文档**  


[大盘列表]()

[配置Prometheus监控采集规则]()

