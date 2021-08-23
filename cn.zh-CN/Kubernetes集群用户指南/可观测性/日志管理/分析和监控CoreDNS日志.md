---
keyword: [CoreDNS, 日志, DNS服务, 监控]
---

# 分析和监控CoreDNS日志

阿里云容器服务ACK部署了CoreDNS作为集群内的DNS服务器，您可以通过查看CoreDNS日志来分析CoreDNS解析慢、访问高危请求域名等问题。本文介绍如何开启CoreDNS日志分析与监控能力。

-   已安装日志组件logtail-ds。

    在创建集群时，ACK默认选中日志组件logtail-ds。如果您未安装日志组件logtail-ds，关于如何手动安装的操作，请参见[通过日志服务采集Kubernetes容器日志](/cn.zh-CN/Kubernetes集群用户指南/可观测性/日志管理/通过日志服务采集Kubernetes容器日志.md)。

-   请确保日志应用alibaba-log-controller版本不低于0.2.0.0-76648ee-aliyun。

    ![logcontroller](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/2169869261/p309799.png)

    如果alibaba-log-controller版本低，可直接升级logtail-ds组件。关于如何升级组件，请参见[管理组件](/cn.zh-CN/Kubernetes集群用户指南/组件/管理组件.md)。


## 步骤一：开启CoreDNS日志服务

**说明：** 开启CoreDNS日志会消耗额外的CPU（约10%，与实际请求有关）及网络流量。如果当前已经部署的CoreDNS副本CPU使用量较高，您可以对CoreDNS进行扩容。关于如何扩容CoreDNS副本数，请参见[手动伸缩容器组](/cn.zh-CN/Kubernetes集群用户指南/应用/工作负载/管理容器组（Pod）.md)。

在命名空间kube-system下，ACK集群有一个**coredns**配置项。您可以通过在**coredns**配置项中的Corefile字段中加上log插件，开启CoreDNS每次域名解析的日志。关于如何修改配置项的具体步骤，请参见[t15755.dita\#task\_1797447/section\_2px\_yjo\_j61](t15755.dita#task_1797447/section_2px_yjo_j61)。

默认日志格式的**coredns**的配置如下。

```
  Corefile: |
    .:53 {
        errors
        log // 此处添加Log插件。
        health {
           lameduck 5s
        }
        ready
        kubernetes cluster.local in-addr.arpa ip6.arpa {
          pods insecure
          upstream
          fallthrough in-addr.arpa ip6.arpa
          ttl 30
        }
        prometheus :9153
        forward . /etc/resolv.conf
        cache 30
        loop
        reload
        loadbalance
    }
    // 如果有其它的域，且该域容器需要被采集日志，也需要增加Log插件，格式一致。
    demo.com:53 {
        ... 
        log //此处添加Log插件。
    }
```

## 步骤二：配置CoreDNS日志采集

日志采集配置针对ACK进行了自定义资源CRD扩展，您可以直接部署AliyunLogConfig的CRD配置，Log Controller会自动创建日志服务相关采集配置和报表资源。关于如何创建自定义资源AliyunLogConfig的具体步骤，请参见[管理自定义资源](/cn.zh-CN/Kubernetes集群用户指南/应用/工作负载/管理自定义资源.md)。

创建自定义资源AliyunLogConfig使用的YAML配置文件如下。

```
apiVersion: log.alibabacloud.com/v1alpha1
kind: AliyunLogConfig
metadata:
  #     Your config name, must be unique in you k8s cluster.
  name: k8s-coredns-log
  namespace: kube-system
spec:
  # logstore name to upload log
  logstore: coredns-log
  # logtail config detail
  productCode: k8s-coredns
  logtailConfig:
    inputType: plugin
    # logtail config name, should be same with [metadata.name]
    configName: k8s-coredns-log
    inputDetail:
      plugin:
        inputs:
        - type: service_docker_stdout
          detail:
            IncludeLabel:
              io.kubernetes.container.name: coredns
            Stderr: true
            Stdout: true
        processors:
        - type: processor_regex
          detail:
            KeepSource: false
            KeepSourceIfParseError: true
            Keys:
            - level
            - remote
            - port
            - id
            - type
            - class
            - name
            - proto
            - size
            - do
            - bufsize
            - rcode
            - rflags
            - rsize
            - duration
            NoKeyError: true
            NoMatchError: false
            FullMatch: false
            Regex: \[([^]]+)]\s([^:]+):(\S+)\s+-\s+(\S+)\s+"(\S+)\s+(\S+)\s+(\S+)\s+(\S+)\s+(\S+)\s+(\S+)\s+([^"]+)"\s+(\S+)\s+(\S+)\s+(\S+)\s+([\d\.]+).*
            SourceKey: content
        - type: processor_regex
          detail:
            KeepSource: false
            KeepSourceIfParseError: true
            Keys:
            - error
            - rcode
            - name
            - type
            - errorMsg
            NoKeyError: false
            NoMatchError: false
            FullMatch: false
            Regex: \[ERROR]\s+(plugin/errors):\s+(\S)+\s+(\S+)\s+([^:]*):\s+(.*)
            SourceKey: content
```

**说明：**

-   请确保日志组件alibaba-log-controller版本不低于0.2.0.0-76648ee-aliyun。如果在更新alibaba-log-controller版本前，您已经部署了该CRD配置，请删除该配置并重新部署。
-   上述配置只针对默认CoreDNS中的日志格式生效。如果您自定义了CoreDNS的日志格式，请修改上述CRD配置中的正则表达式Regex部分。

    关于自定义CoreDNS的日志格式的更多信息，请参见[log](https://coredns.io/plugins/log/)。

    关于更详细的日志采集流程及采集配置，请参见[通过DaemonSet-CRD方式采集日志](/cn.zh-CN/数据采集/Logtail采集/采集容器日志/通过DaemonSet-CRD方式采集日志.md)。


## 步骤三：查看CoreDNS日志采集

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群信息页面，单击**集群资源**页签，然后单击**日志服务Project**右侧的日志服务Project名称链接。

5.  在日志库页面左侧导航栏，单击![resource](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/6958619161/p267638.png)图标，然后单击**仪表盘**，在仪表盘列表中找到并单击**Kubernetes CoreDNS日志分析**。

    在**Kubernetes CoreDNS日志分析**页面，您可以查看CoreDNS请求数、请求成功率和请求延迟等聚合信息，同时提供了各类Top域名请求列表、错误域名列表、慢解析域名列表和高危请求域名访问分析。

    ![coredns log](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/4589619161/p267483.png)


## 步骤四：创建CoreDNS日志告警

在**Kubernetes CoreDNS日志分析**仪表盘页面中，您可以根据仪表盘中单个图表进行告警设置。

在目标图表卡片右上方，选择**![more](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/4484619161/p267496.png)** \> **另存为告警（新版）**。关于告警配置的参数设置，请参见[创建日志告警监控规则](/cn.zh-CN/告警/告警（新版）/告警监控/创建日志告警监控规则.md)。

![alert](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/1920969261/p267588.png)

创建完成告警规则后，您可以查看、修改告警规则，关闭告警规则等。更多信息，请参见[告警监控规则相关操作](/cn.zh-CN/告警/告警（新版）/告警监控/告警监控规则相关操作.md)。

**相关文档**  


[基于DNS的服务发现概述](/cn.zh-CN/Kubernetes集群用户指南/网络/服务发现DNS/基于DNS的服务发现概述.md)

[ACK集群内DNS原理和配置说明](/cn.zh-CN/Kubernetes集群用户指南/网络/服务发现DNS/ACK集群内DNS原理和配置说明.md)

