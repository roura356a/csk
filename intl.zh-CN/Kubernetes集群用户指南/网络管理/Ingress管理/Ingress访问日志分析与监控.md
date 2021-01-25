---
keyword: [Ingress, 日志服务, 访问日志分析, 监控]
---

# Ingress访问日志分析与监控

阿里云Ingress组件除了提供外部可访问的URL、负载均衡、SSL、基于名称的虚拟主机外，还支持将所有您的HTTP请求日志记录到标准输出中。您可以在创建集群时启用日志服务，在阿里云日志服务SLS查看Ingress的访问日志分析报表和监控Ingress实时状态。

1.  安装日志组件。

    集群创建时，默认会安装日志组件，如未安装，请参见[通过日志服务采集Kubernetes容器日志](/intl.zh-CN/Kubernetes集群用户指南/可观测性/日志管理/通过日志服务采集Kubernetes容器日志.md)进行手动安装。

2.  升级Log Controller。

    通过执行`kubectl edit deployment alibaba-log-controller -n kube-systemx`命令修改镜像版本进行升级。


## 部署Ingress采集配置

日志采集配置针对ACK进行了CRD扩展，您可以直接部署AliyunLogConfig的CRD配置，Log Controller会自动创建日志服务相关采集配置和报表资源。

执行以下命令，部署AliyunLogConfig的CRD配置。

```
cat <<-EOF | kubectl apply -n kube-system -f -
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
            - proxy_alternative_upstream_name
            NoKeyError: true
            NoMatchError: true
            Regex: ^(\S+)\s-\s\[([^]]+)]\s-\s(\S+)\s\[(\S+)\s\S+\s"(\w+)\s(\S+)\s([^"]+)"\s(\d+)\s(\d+)\s"([^"]*)"\s"([^"]*)"\s(\S+)\s(\S+)+\s\[([^]]*)]\s(\S+)\s(\S+)\s(\S+)\s(\S+)\s(\S+)\s*(\S*)\s*\[*([^]]*)\]*.*
            SourceKey: content
EOF
```

**说明：**

-   请确保日志组件alibaba-log-controller版本不低于`0.2.0.0-76648ee-aliyun`。更新版本后，若已经应用了该CRD配置，请删除该配置并重新应用。
-   上述配置只针对ACK默认Ingress Controller中的日志格式生效，若您修改过Ingress Controller的访问日志格式，请根据[通过DaemonSet-CRD方式采集日志](/intl.zh-CN/数据采集/Logtail采集/采集容器日志/通过DaemonSet-CRD方式采集日志.md)修改上述CRD配置中的正则表达式提取`processor_regex`部分。

## 查看Ingress日志与报表

1.  登录[日志服务控制台](https://sls.console.aliyun.com)。

2.  在Project列表区域，选择创建集群时设置的日志Project，单击名称进入日志Project页面（默认创建的project名称为**k8s-log-\{cluster-id\}**）。

3.  在日志库页面左侧导航栏选择**nginx-ingress** \> **可视化仪表盘**，查看所有Ingress的分析报表。


## Ingress概览

Ingress概览报表主要展示当前Ingress的整体状态，主要包括以下几类信息：

-   整体架构状态（1天），包括PV、UV、流量、响应延迟、移动端占比和错误比例等。
-   网站实时状态（1分钟），包括访问PV、访问UV、访问成功率、平均延迟、P95延迟和P99延迟等。
-   用户请求类信息（1天），包括1天访问PV对比、7天访问访问PV对比、地域分布、TOP访问省份、TOP访问城市、移动端占比和Android/IOS占比等。
-   TOPURL统计（1小时），包括访问TOP10、延迟TOP10、5XX错误TOP10和404错误TOP10。

![ingress概览](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8973321161/p40696.png)

## Ingress访问中心

Ingress访问中心主要侧重于用于访问请求相关的统计信息，一般用于运营分析，包括今日UV、今日PV、UV分布、PV分布、TOP访问省份、TOP访问城市、TOP访问浏览器、TOP访问IP、移动端占比和Android/IOS占比等。

![ingress访问中心](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7285659951/p40704.png)

## Ingress监控中心

Ingress监控中心主要侧重于网站实时监控数据，一般用于完整实时监控与告警，包括请求成功率、错误比例、5XX比例、请求未转发比例、平均延迟、P95延迟、P99延迟、P999延迟、状态码分布、Ingress压力分布、Service访问TOP10、Service错误TOP10、Service延迟TOP10和Service流量TOP10等。

![Ingress监控中心](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8285659951/p40705.png)

## Ingress蓝绿发布监控中心

Ingress蓝绿发布监控中心主要用于版本发布时的实时监控与对比（版本前后对比以及蓝绿版本当前对比），以便您在服务发布时快速检测异常并进行回滚。在该报表中您需要选择进行对比的蓝绿版本（**ServiceA**和**ServiceB**），报表将根据您的选择动态显示蓝绿版本相关指标，包括PV、5XX比例、成功率、平均延迟、P95延迟、P99延迟、P999延迟和流量等。

![Ingress蓝绿发布监控中心](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8285659951/p40706.png)

## Ingress异常检测中心

Ingress异常检测中心基于日志服务提供的机器学习算法，通过多种时序分析算法从Ingress的指标中自动检测异常点，提高问题发现的效率。

![Ingress异常检测中心](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8285659951/p40707.png)

## 配置告警

日志服务除提供交互式分析、可视化报表之外，您可直接基于上述报表快速配置告警，告警支持通知钉钉WebHook、短信、邮件和自定义WebHook等。

告警详细配置方法请参见[设置告警](/intl.zh-CN/可视化与告警/告警/设置告警.md)。

下述示例为Ingress配置5XX比例的告警，告警每5分钟执行一次，当5XX比例超过1%时触发。

1.  进入Ingress监控中心报表，鼠标滑动到图表**5XX比例**右上角的![提示](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8973321161/p229419.png)，在弹出的提示框中单击**创建告警**。

    ![配置告警](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9285659951/p40708.png)

2.  在**告警配置**页签中，填入**告警名称**、**查询区间**、**频率**、**触发条件**，查询语句中的`total`为5XX的百分比，因此触发条件填入：`total > 1`。

    ![设置告警](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9285659951/p40709.png)

3.  在下一步的**通知**选项中，根据您的需求选择对应的通知方式，并填入对应参数，然后单击**提交**，即可完成告警创建。


## 订阅定时报告

日志服务除支持通过告警方式通知外，还支持报表订阅功能，您可使用该功能将报表定期渲染成图片并通过邮件、钉钉群等方式发送。

订阅功能详细使用方法请参见[订阅仪表盘](/intl.zh-CN/可视化与告警/仪表盘/订阅仪表盘.md)。

下述示例为Ingress概览配置订阅功能，每天上午10点将报表发送到指定钉钉群。

1.  进入Ingress概览V1.2报表，选择报表右上角的**订阅** \> **创建**。

2.  在弹出的配置页面中，频率选择每天、10:00，关闭**添加水印**选项，单击**下一步**。

3.  通知类型中选择**WebHook钉钉机器人**，填入钉钉机器人的WebHook地址，然后单击**提交**，即可完成订阅。


