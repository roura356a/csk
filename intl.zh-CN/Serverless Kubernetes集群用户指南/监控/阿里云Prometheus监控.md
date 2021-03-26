---
keyword: 阿里云Prometheus监控
---

# 阿里云Prometheus监控

您可以通过阿里云Prometheus监控查看Serverless Kubernetes（ASK）集群预先配置的监控大盘和监控性能指标。本文为您介绍如何在ASK中接入阿里云的Prometheus监控。

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

    -   阿里云Prometheus监控支持开源组件监控。您还可以输入阿里云的RDS、Redis组件的账号和密码，阿里云Prometheus监控即可为您默认生成这些组件的Exporter，并为您创建默认的组件大盘。您的时间成本可由原来使用开源Prometheus监控开源组件的7天左右降低至3分钟左右。

        ![监控大盘2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7095659951/p103081.png)

    -   阿里云Prometheus监控支持一键安装、一键卸载，以及通过健康检查功能调试Prometheus监控。您的时间成本可由原来使用开源Prometheus监控的1天左右降低至3分钟左右。

## 开启阿里云Prometheus监控

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

3.  在应用目录页面的**阿里云应用**页签中，搜索并单击**ack-arms-prometheus**。

4.  在应用目录 - ack-arms-prometheus右侧的**创建**区域中，选择目标集群，然后单击**创建**。

    **说明：** **命名空间**和**发布名称**默认为**arms-prom**。


在控制台左侧导航栏选择**集群**。在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。在**概览**页签下单击右上角的**prometheus监控**，跳转到prometheus控制台。在**监控列表**页面找到目标集群并单击已安装的插件，进入大盘查看监控数据。

