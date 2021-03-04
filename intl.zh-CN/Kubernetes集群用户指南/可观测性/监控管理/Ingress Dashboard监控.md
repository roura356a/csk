# Ingress Dashboard监控

阿里云Ingress组件支持将您的所有HTTP请求日志记录到标准输出中。同时，阿里云打通Ingress组件访问日志服务与阿里云日志服务，从而您可以使用日志服务快速创建日志分析和监控大盘。本文介绍如何使用Ingress Dashboard与ARMS进行联动排查。

实现Ingress Dashboard监控前，您需要先安装日志组件。以下为安装日志组件的两种方法：

-   如果您还没有创建集群，您可以在创建集群时，选中**使用日志服务**、**安装Ingress 组件**以及**创建Ingress Dashboard**。
-   如果您已创建集群，您需要在组件管理页面安装日志组件。更多信息，请参见[通过日志服务采集Kubernetes容器日志](/intl.zh-CN/Kubernetes集群用户指南/可观测性/日志管理/通过日志服务采集Kubernetes容器日志.md)。

## 步骤一：查看Ingress日志与报表

1.  登录[日志服务控制台](https://sls.console.aliyun.com)。

2.  在**Project列表**区域，选择创建集群时设置的日志Project，单击名称进入日志Project页面（默认创建的project名称为**k8s-log-\{cluster-id\}**）。

3.  在Project详情页面**日志库**页签下单击**nginx-ingress**左侧的![仪表盘](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3603718061/p201103.png)。

    **说明：** 名称为**nginx-ingress**的日志库（logstore）存放着所有的Ingress访问日志。

4.  单击**nginx-ingress**下的**可视化仪表盘**，查看所有Ingress的分析报表。

    Ingress Dashboard包含Ingress概览、Ingress异常检测中心、Ingress访问中心、Ingress蓝绿发布中心、Ingress监控中心5个预置图表。以下重点介绍Ingress概览和Ingress蓝绿发布中心。关于其他预置图表的详细信息请参见[Ingress访问日志分析与监控](/intl.zh-CN/Kubernetes集群用户指南/网络管理/Ingress管理/Ingress访问日志分析与监控.md)。

    **Ingress概览**

    Ingress概览报表主要展示当前Ingress的整体状态，主要包括以下几类信息：

    -   整体架构状态（1天），包括PV、UV、流量、响应延迟、移动端占比和错误比例等。
    -   网站实时状态（1分钟），包括PV、UV、成功率、5XX比例、平均延迟和P95/P99延迟等。
    -   用户请求类信息（1天），包括1天/7天访问PV对比、访问地域分布、TOP访问省份/城市、移动端占比和Android/IOS占比等。
    -   TOPURL统计（1小时），包括访问TOP10、延迟TOP10、5XX错误TOP10和404错误TOP10。
    ![Ingress概览](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3603718061/p201125.png)

    **Ingress蓝绿发布监控中心**

    Ingress蓝绿发布监控中心主要用于版本发布时的实时监控与对比（版本前后对比以及蓝绿版本当前对比），以便您在服务发布时快速检测异常并进行回滚。在该报表中您需要选择进行对比的蓝绿版本（**ServiceA**和**ServiceB**），报表将根据您的选择动态显示蓝绿版本相关指标，包括PV、5XX比例、成功率、平均延迟、P95/P99/P9999延迟和流量等。

    ![监控中心](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3603718061/p201128.png)


## 步骤二：为Java应用开启ARMS应用监控

在创建新应用的同时开启ARMS应用监控，请按以下步骤操作：

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

5.  在无状态页签右上角单击**使用模板创建**。

6.  在使用模板创建页面上选择**示例模板**，并在**模板**（YAML格式）中将以下`annotations`添加到spec \> template \> metadata层级下。

    **说明：** 请将<your-deployment-name\>替换为您的应用名称。

    ```
    annotations:
      armsPilotAutoEnable: "on"
      armsPilotCreateAppName: "<your-deployment-name>"                                
    ```

    ![YAML Example](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0446760061/p53707.png)

    创建一个无状态（Deployment）应用并开启ARMS应用监控的完整YAML示例模板如下：


## 步骤三：为示例服务配置Ingress

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在**路由**页面单击**创建**。

5.  在**创建**对话框中设置路由，然后单击**创建**。

    关于设置路由的详细操作，请参见[Ingress基本操作](/intl.zh-CN/Kubernetes集群用户指南/网络管理/Ingress管理/Ingress基本操作.md)。


## 步骤四：使用Ingress Dashboard与ARMS进行联动排查

1.  登录[日志服务控制台](https://sls.console.aliyun.com)。

2.  在**Project列表**区域，选择创建集群时设置的日志Project，单击名称进入日志Project页面（默认创建的project名称为**k8s-log-\{cluster-id\}**）。

3.  在Project详情页面，默认进入日志库页面。名称为**nginx-ingress**的日志库（logstore）存放着所有的Ingress访问日志。单击左侧导航栏中的**可视化仪表盘**。

4.  在仪表盘列表中单击**Ingress概览V1.2**。

5.  在**Ingress概览V1.2**页面找到**Top10延迟URL**，查看TOP10延迟URL。

6.  在**Ingress概览V1.2**页面单击**URL（ARMS排查）**，跳转到对应服务的ARMS监控链路查询页面。您可以在ARMS控制台查询对应服务的查询调用链路详细情况。

    ![ARMS排查](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6763197061/p199045.png)


