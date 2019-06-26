# Ingress 访问日志分析与监控 {#concept_nmh_nks_vdb .concept}

阿里云Ingress除了提供外部可访问的 URL、负载均衡、SSL、基于名称的虚拟主机外，还支持将所有用户的HTTP请求日志记录到标准输出中。同时Ingress访问日志与阿里云日志服务打通，您可以使用日志服务快速创建日志分析和监控大盘。

## 前提条件 {#section_chy_qqs_vdb .section}

1.  安装日志组件。

    集群创建时，默认会安装日志组件，如未安装，请参考 [使用日志服务进行Kubernetes日志采集](intl.zh-CN/用户指南/Kubernetes集群/日志管理/使用日志服务进行Kubernetes日志采集.md#)进行手动安装。

2.  升级Log Controller。

    升级kube-system命名空间下的无状态应用（Deployment）alibaba-log-controller，替换以下内容：

    -   镜像名称：registry-vpc.\{region-id\}.aliyuncs.com/acs/log-controller ，将其中的`{region-id}`替换为您集群所在Region ID，例如cn-hangzhou、cn-beijing、ap-southeast-1等。
    -   镜像版本（Tag）：版本不低于0.2.0.0-76648ee-aliyun。
    以下两种升级方法您可任选其一：

    -   通过执行kubectl命令`kubectl edit deployment alibaba-log-controller -n kube-system`进行升级。
    -   通过容器服务控制台升级。进入**应用** \> **无状态（Deployment）**，选择对应集群的命名空间**kube-system**，编辑**alibaba-log-controller**并保存。

## 部署Ingress采集配置 {#section_g4b_sqs_vdb .section}

``` {#codeblock_1k1_sf6_qd0}
apiVersion: log.alibabacloud.com/v1alpha1
kind: AliyunLogConfig
metadata:
  # your config name, must be unique in you k8s cluster
  name: k8s-nginx-ingress
spec:
  # logstore name to upload log
  logstore: nginx-ingress
  # product code, only for k8s nginx ingress
  productCode: k8s-nginx-ingress
  # logtail config detail
  logtailConfig:
    inputType: plugin
    # logtail config name, should be same with [metadata.name]
    configName: k8s-nginx-ingress
    inputDetail:
      plugin:
        inputs:
        - type: service_docker_stdout
          detail:
            IncludeLabel:
              io.kubernetes.container.name: nginx-ingress-controller
            Stderr: false
            Stdout: true
        processors:
        - type: processor_regex
          detail:
            KeepSource: false
            Keys:
            - client_ip
            - x_forward_for
            - remote_user
            - time
            - method
            - url
            - version
            - status
            - body_bytes_sent
            - http_referer
            - http_user_agent
            - request_length
            - request_time
            - proxy_upstream_name
            - upstream_addr
            - upstream_response_length
            - upstream_response_time
            - upstream_status
            - req_id
            - host
            NoKeyError: true
            NoMatchError: true
            Regex: ^(\S+)\s-\s\[([^]]+)]\s-\s(\S+)\s\[(\S+)\s\S+\s"(\w+)\s(\S+)\s([^"]+)"\s(\d+)\s(\d+)\s"([^"]*)"\s"([^"]*)"\s(\S+)\s(\S+)+\s\[([^]]*)]\s(\S+)\s(\S+)\s(\S+)\s(\S+)\s(\S+)\s*(\S*).*
            SourceKey: content
```

日志采集配置针对Kubernetes进行了CRD扩展，可直接部署AliyunLogConfig的CRD配置，Log Controller会自动创建日志服务相关采集配置和报表资源，部署方式任选其一：

**说明：** 

-   请确保日志组件 alibaba-log-controller版本不低于`0.2.0.0-76648ee-aliyun`。更新版本后，若已经应用了该CRD配置，请删除该配置并重新应用。
-   上述配置只针对阿里云Kubernetes默认Ingress Controller中的日志格式生效，若您修改过Ingress Controller的访问日志格式，请根据[Kubernetes-CRD配置日志采集](../../../../intl.zh-CN/用户指南/Logtail采集/容器日志采集/Kubernetes-CRD配置日志采集.md#)修改上述CRD配置中的正则表达式提取`processor_regex`部分。

-   使用Kubectl命令部署

    将上述CRD配置保存成nginx-ingress.yaml ，执行 kubectl apply -f命令进行部署。

-   使用编排模板部署
    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
    2.  将上述CRD配置保存成编排模板。

        编排模板文档请参见[创建编排模板](intl.zh-CN/用户指南/Kubernetes集群/模板管理/创建编排模板.md#) 

    3.  基于该模板创建应用，选择您所在集群的default**命名空间**。

## 查看Ingress日志与报表 {#section_msy_bhq_bhb .section}

1.  登录[日志服务控制台](https://sls.console.aliyun.com)。
2.  单击左侧导航栏中的**Project管理**，选择创建集群时设置的日志Project，单击名称进入日志Project页面（默认创建的project名称为 **k8s-log-\{cluster-id\}**）。
3.  在Project详情页面，默认进入日志库页面。名称为**nginx-ingress**的日志库（logstore）存放着所有的Ingress访问日志。单击左侧导航栏中的**仪表盘**进入仪表盘列表，可查看到所有Ingress的分析报表。

## Ingress概览 {#section_rnq_vnq_bhb .section}

Ingress概览报表主要展示当前Ingress的整体状态，主要包括以下几类信息：

-   整体架构状态（1天），包括PV、UV、流量、响应延迟、移动端占比和错误比例等。
-   网站实时状态（1分钟），包括PV、UV、成功率、5XX比例、平均延迟和P95/P99延迟等。
-   用户请求类信息（1天），包括1天/7天访问PV对比、访问地域分布、TOP访问省份/城市、移动端占比和Android/IOS占比等。
-   TOPURL统计（1小时），包括访问TOP10、延迟TOP10、5XX错误TOP10和404错误TOP10。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16678/156154274640696_zh-CN.png)

## Ingress访问中心 {#section_e2b_1pq_bhb .section}

Ingress访问中心主要侧重于用于访问请求相关的统计信息，一般用于运营分析，包括当日UV/PV、UV/PV分布、UV/PV趋势、TOP访问省份/城市、TOP访问浏览器、TOP访问IP、移动端占比和Android/IOS占比等。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16678/156154274740704_zh-CN.png)

## Ingress监控中心 {#section_bl3_2pq_bhb .section}

Ingress监控中心主要侧重于网站实时监控数据，一般用于完整实时监控与告警，包括请求成功率、错误比例、5XX比例、请求未转发比例、平均延迟、P95/P99/P9999延迟、状态码分布、Ingress压力分布、Service访问TOP10、Service错误TOP10、Service延迟TOP10和Service流量TOP10等。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16678/156154274740705_zh-CN.png)

## Ingress蓝绿发布监控中心 {#section_ny3_hpq_bhb .section}

Ingress蓝绿发布监控中心主要用于版本发布时的实时监控与对比（版本前后对比以及蓝绿版本当前对比），以便您在服务发布时快速检测异常并进行回滚。在该报表中您需要选择进行对比的蓝绿版本（ServiceA和ServiceB），报表将根据您的选择动态显示蓝绿版本相关指标，包括PV、5XX比例、成功率、平均延迟、P95/P99/P9999延迟和流量等。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16678/156154274840706_zh-CN.png)

## Ingress异常检测中心 {#section_rst_jpq_bhb .section}

Ingress异常检测中心基于日志服务提供的机器学习算法，通过多种时序分析算法从Ingress的指标中自动检测异常点，提高问题发现的效率。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16678/156154274840707_zh-CN.png)

## 配置告警 {#section_ozp_mpq_bhb .section}

日志服务除提供交互式分析、可视化报表之外，您可直接基于上述报表快速配置告警，告警支持通知钉钉WebHook、短信、邮件和自定义WebHook等。

告警详细配置方法请参考[设置告警](../../../../intl.zh-CN/用户指南/告警/设置告警任务/设置告警.md#)。

下述示例为Ingress配置5XX比例的告警，告警每5分钟执行一次，当5XX比例超过1%时触发。

1.  进入Ingress监控中心报表，鼠标滑动到图表**5XX比例**的右上角，在弹出的提示框中单击**新建告警**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16678/156154274840708_zh-CN.png)

2.  在告警页面中，填入**告警名称**、**查询区间**和**执行间隔**，查询语句中的`total`为5XX的百分比，因此触发条件填入：`total > 1`。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16678/156154274840709_zh-CN.png)

3.  在下一步的**通知**选项中，根据您的需求选择对应的通知方式，并填入对应参数即可完成告警创建。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16678/156154274840710_zh-CN.png)


## 订阅定时报告 {#section_hn2_2rq_bhb .section}

日志服务除支持通过告警方式通知外，还支持报表订阅功能，您可使用该功能将报表定期渲染成图片并通过邮件、钉钉群等方式发送。

订阅功能详细使用方法请参考[订阅仪表盘](../../../../intl.zh-CN/用户指南/可视化分析/仪表盘/订阅仪表盘.md#)。

下述示例为Ingress概览配置订阅功能，每天上午10点将报表发送到指定钉钉群：

1.  进入Ingress概览报表，单击报表右上角的**订阅**按钮。
2.  在弹出的配置页面中，频率选择每天、10:00，关闭**添加水印**选项。
3.  通知类型中选择**钉钉机器人**，填入钉钉机器人的WebHook地址（WebHook地址请参见[自定义机器人](https://open-doc.dingtalk.com/docs/doc.htm?treeId=257&articleId=105735&docType=1)获取）即可完成订阅。

