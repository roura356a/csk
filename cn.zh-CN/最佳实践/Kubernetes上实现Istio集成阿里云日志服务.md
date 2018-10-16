# Kubernetes上实现Istio集成阿里云日志服务 {#concept_dmz_rbt_cfb .concept}

阿里云Kubernetes容器服务已经提供了Istio与日志服务Log Service的集成能力，本文通过一个官方示例来重点介绍Istio与基于阿里云日志服务的分布式追踪系统的整合能力。

**说明：** 在使用阿里云Kubernetes容器服务Istio 1.0的过程中，如果遇到类似CRD版本问题，请参考我们提供的[问题分析](https://yq.aliyun.com/articles/621026)。 我们会持续更新遇到的问题及其解决方法。

## 前提条件 {#section_llz_ljq_kfb .section}

-   您已成功部署一个Kubernetes集群，参见[创建Kubernetes集群](../../../../cn.zh-CN/用户指南/Kubernetes 集群/集群管理/创建Kubernetes集群.md#)。
-   您需要拥有一个本地Linux环境，并已配置好Kubectl工具连接到集群，参见[通过 kubectl 连接 Kubernetes 集群](../../../../cn.zh-CN/用户指南/Kubernetes 集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)。
-   您已下载对应Istio版本的项目代码，并在Istio文件目录下执行相关命令。参见[https://github.com/istio/istio/releases](https://github.com/istio/istio/releases)。
-   您已成功部署测试应用 BookInfo，参见[安装官方示例](cn.zh-CN/最佳实践/在Kubernetes上基于Istio实现Service Mesh智能路由.md#section_csj_jln_cfb)或者[BookInfo指南](https://istio.io/docs/examples/bookinfo/)。

## 回顾 OpenTracing {#section_fsj_vct_cfb .section}

为了解决不同的分布式追踪系统 API 不兼容的问题，诞生了 OpenTracing 规范。OpenTracing 是一个轻量级的标准化层，它位于应用程序/类库和追踪或日志分析程序之间。OpenTracing 已进入 CNCF，正在为全球的分布式追踪，提供统一的概念和数据标准。它通过提供平台无关、厂商无关的 API，使得开发人员能够方便添加（或更换）追踪系统的实现。

Jaeger 是CNCF下的一款开源分布式追踪系统，兼容 OpenTracing API。

## 阿里云日志服务Log Service与分布式追踪系统Jaeger {#section_gsj_vct_cfb .section}

日志服务（Log Service，简称LOG/原SLS）是针对实时数据一站式服务，在阿里集团经历大量大数据场景锤炼而成。提供日志类数据采集、消费、投递及查询分析功能，全面提升海量日志处理/分析能力。

Jaeger 是 Uber 推出的一款开源分布式追踪系统，为微服务场景而生。它主要用于分析多个服务的调用过程，图形化服务调用轨迹，是诊断性能问题、分析系统故障的利器。

Jaeger on Aliyun Log Service 是基于 Jeager 开发的分布式追踪系统，支持将采集到的追踪数据持久化到阿里云日志服务中，并通过 Jaeger 的原生接口进行查询和展示。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21299/153966924612607_zh-CN.png)

|组件|说明|
|--|--|
|Jaeger-client|Jaeger-client 为不同语言实现了符合 OpenTracing 标准的 SDK。应用程序通过 API 写入数据，客户端库library 把 trace 信息按照应用程序指定的采样策略传递给 jaeger-agent。数据使用 Thrift 序列化，通过 UDP 进行通信。|
|Jaeger-agent|Jaeger-agent 是一个监听在 UDP 端口上接收 span 数据的网络守护进程，它会将数据批量发送给Jaeger-collector。它被设计成一个基础组件，部署到所有的宿主机上。Jaeger-agent代理将 Jaeger-client 和 Jaeger-collector 解耦，为 Jaeger-client library 屏蔽了路由和发现 collector 的细节。|
|Jaeger-collector|接收 jaeger-agent 发送来的数据，然后将数据写入后端存储。后端存储是一个可插拔的组件，Jaeger on Aliyun Log Service 增加了对阿里云日志服务的支持。|
|阿里云日志服务Log Service|Jaeger-collector 会将接收到的 span 数据持久化到日志服务Log Service中。Query 会从日志服务中检索数据。|
|Query&UI|接收查询请求，从后端存储系统中检索 trace 并通过 UI 进行展示。|

## 步骤1 通过Web界面一键部署Istio {#section_osj_vct_cfb .section}

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  单击左侧导航栏的**集群**，选择所需集群，单击**更多** \> **部署Istio**。

    **说明：** 本例中启用阿里云日志服务 SLS 及 Jaeger，更多信息参见[部署Istio](../../../../cn.zh-CN/用户指南/Kubernetes 集群/Istio管理/部署Istio.md#)，Isito的版本是1.0。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21299/153966924612608_zh-CN.png)

3.  在部署 Istio 页面中，选择**启用阿里云日志服务 SLS 及 Jaeger**。

    阿里云Kubernetes容器服务中整合了日志服务Log Service， 其分布式追踪数据会保存到阿里云日志服务Log Store中。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21299/153966924613746_zh-CN.png)

    启用日志服务的参数说明如下:

    |参数名|对应参数|描述|
    |---|----|--|
    |**服务入口地址（Endpoint）**|storage.aliyun\_sls.endpoint|指定用于存储 Span 的 Project 所在的 Endpoint|
    |**项目名称（Project）**|storage.aliyun\_sls.project|指定用于存储 Span 的 Project，项目名称只能包含小写字母、数字和连字符（-），且必须以小写字母和数字开头和结尾，长度为 3~63 个字节|
    |**日志库名称（Logstore）**|storage.aliyun\_sls.logstore|指定用于存储 Span 的 Logstore，须由小写字母、数字、连字符（-）和下划线（\_）组成，且以小写字母或者数字开头和结尾，长度为3-63字节。Logstore 名称在其所属项目内必须唯一|
    |**AccessKeyID**|storage.aliyun\_sls.accesskey.id|指定用户标识 Access Key ID|
    |**AccessKeySecret**|storage.aliyun\_sls.accesskey.secret|指定用户标识 Access Key Secret|

    **说明：** 如果指定的Project不存在，系统会自动创建一个新project，并且完成相应的初始化工作。

4.  最后单击**部署 Istio**。几分钟之后，一套集成阿里云日志服务的Istio实例就已创建完毕。
5.  单击左侧导航栏的**应用** \> **服务**，在右侧可以查看到刚创建的Istio相关服务，其中包括集成的日志相关服务。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21299/153966924612692_zh-CN.png)


## 步骤2 查看分布式调用链跟踪Jaeger on Aliyun Log Service {#section_ewt_4fg_mfb .section}

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  单击左侧导航栏的**应用** \> **服务**，选择所需集群和istio-system命名空间，单击tracing-on-sls-query服务的外部地址，进入Jaeger UI 页面。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21299/153966924612696_zh-CN.png)

3.  在左侧选择**Service**，然后选择各项配置，最后单击**Find Traces**，右上角显示的时刻和持续时间散点图用可视化方式呈现了结果，并提供了向下挖掘能力。

    **说明：** 本例中以BookInfo示例应用为例，查看productpage服务的调用情况。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21299/153966924612697_zh-CN.png)

4.  用户可以选择用多种不同视图对追踪结果进行可视化，例如追踪时段内的直方图，或服务在追踪过程中的累积时间。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21299/153966924612698_zh-CN.png)


## 步骤3 查看阿里云日志服务Log Service {#section_etj_vct_cfb .section}

1.  登录 [日志服务管理控制台](https://sls.console.aliyun.com/)。
2.  单击左侧导航栏中**Project管理**，选择所需的Project，并单击Project项目名称。
3.  进入Project详情页，单击**查询**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21299/153966924712699_zh-CN.png)

4.  选择日志查询时间，然后单击**查询/分析**。

    日志服务查询分析功能除了提供日志内容的各种语句查询能力以外，还提供多种扩展功能优化您的查询，如支持各种图表功能。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21299/153966924712700_zh-CN.png)

    统计图表：

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21299/153966924712701_zh-CN.png)


## 总结 {#section_gtj_vct_cfb .section}

阿里云Kubernetes容器服务已经提供了Istio与日志服务Log Service的集成能力，本文通过一个官方示例来重点介绍了Istio与基于阿里云日志服务的分布式追踪系统的整合能力。

欢迎大家使用阿里云上的容器服务，快速搭建微服务的开放治理平台Istio，比较简单地集成到自己项目的微服务开发中。

