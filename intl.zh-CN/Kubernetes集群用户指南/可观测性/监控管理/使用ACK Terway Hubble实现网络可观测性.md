---
keyword: [ACK Terway Hubble, 容器网络, 网络流量, 网络可观测性]
---

# 使用ACK Terway Hubble实现网络可观测性

在ACK集群中部署ACK Terway Hubble可以将容器网络流量、网络策略进行可视化展示，从而实现网络架构、业务拓扑关系的可观测性。本文介绍如何使用ACK Terway Hubble查看容器网络中网络流量的来源、目的地等数据，实现网络可观测性。

[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)

**说明：** ACK Terway Hubble当前仅支持基于IPvlan的Terway ENI多IP网络模式。因此创建集群时必须设置**网络插件**为**Terway**，**Terway模式**为**IPvlan**，不然无法使用ACK Terway Hubble。

## 步骤一：设置Terway的配置文件eni-config

**通过控制台设置Terway的配置文件eni-config**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  编辑Terway的配置文件eni-config。

    1.  在集群管理页左侧导航栏中，选择**配置管理** \> **配置项**。

    2.  在**配置项**页面顶部设置**命名空间**为kube-system，单击eni-config**操作**列下的**YAML编辑**。

    3.  在**查看YAML**面板找到`10-terway.conf`，在`10-terway.conf`下添加以下参数，然后单击**确定**。

        ```
        "cilium_enable_hubble":"true",
        "cilium_hubble_listen_address":":4244",
        "cilium_hubble_metrics_server":":9091",
        "cilium_hubble_metrics":"drop,tcp,flow,port-distribution,icmp",
        ```

        |参数|描述|备注|
        |--|--|--|
        |eniip\_virtual\_type|是否开启IPVLAN模式。|如果配置文件中此项不存在或不为`IPVLAN`，则您的集群不支持ACK Terway Hubble。|
        |cilium\_enable\_hubble|启用Hubble网络流量分析。|本文设置为`"true"`。|
        |cilium\_hubble\_listen\_address|Hubble网络流量信息暴露地址。|本文设置为`":4244"`。|
        |cilium\_hubble\_metrics\_server|Hubble Metrics暴露的地址。|本文设置为`":9091"`。|
        |cilium\_hubble\_metrics|Hubble需要采集的Metrics，以半角逗号（,）分割。|目前不支持DNS、HTTP这类L7的能力。Hubble支持采集的Metrics全量列表为`"drop,tcp,flow,port-distribution,icmp"`。**说明：** 如果您启用Metrics数量比较多，则会对Hubble性能产生一定影响。 |

5.  重启Terway容器组，使其配置文件生效。

    1.  在集群管理页左侧导航栏中，选择**工作负载** \> **容器组**。

    2.  在**容器组**页面顶部设置**命名空间**为kube-system，在搜索框中搜索terway-eniip，单击terway-eniip-xxx**操作**列下的**删除**。

    3.  在**删除容器组**对话框单击**确定**。

        在**容器组**页面terway-eniip-xxx**状态**列显示**Running**，说明重启容器组成功。

    4.  重复执行以上步骤，删除所有Terway容器组。


**通过命令行设置Terway的配置文件eni-config**

1.  [通过kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl管理Kubernetes集群.md)。

2.  编辑Terway的配置文件eni-config。

    1.  执行以下命令，编辑Terway的配置文件。

        ```
        kubectl -n kube-system edit configmap eni-config
        ```

    2.  将以下内容复制到Terway的配置文件，然后保存。

        ```
        "cilium_enable_hubble":"true",
        "cilium_hubble_listen_address":":4244",
        "cilium_hubble_metrics_server":":9091",
        "cilium_hubble_metrics":"drop,tcp,flow,port-distribution,icmp",
        ```

        |参数|描述|备注|
        |--|--|--|
        |eniip\_virtual\_type|是否开启IPVLAN模式。|如果配置文件中此项不存在或不为`IPVLAN`，则您的集群不支持ACK Terway Hubble。|
        |cilium\_enable\_hubble|启用Hubble网络流量分析。|本文设置为`"true"`。|
        |cilium\_hubble\_listen\_address|Hubble网络流量信息暴露地址。|本文设置为`":4244"`。|
        |cilium\_hubble\_metrics\_server|Hubble Metrics暴露的地址。|本文设置为`":9091"`。|
        |cilium\_hubble\_metrics|Hubble需要采集的Metrics，以半角逗号（,）分割。|目前不支持DNS、HTTP这类L7的能力。Hubble支持采集的Metrics全量列表为`"drop,tcp,flow,port-distribution,icmp"`。**说明：** 如果您启用Metrics数量比较多，则会对Hubble性能产生一定影响。 |

3.  重启Terway容器组，使其配置文件生效。

    1.  执行以下命令，查看Terway容器组。

        ```
        kubectl -n kube-system get pod | grep terway-eniip
        ```

    2.  执行以下命令，删除Terway容器组。

        ```
         kubectl -n kube-system delete pod terway-eniip-xxx
        ```

        其中terway-eniip-xxx需替换为Terway容器组名称，重复执行上述步骤，逐个删除容器组。


## 步骤二：安装ACK Terway Hubble

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

3.  在**应用目录**页面搜索ack-terway-hubble，找到并单击ack-terway-hubble。

4.  在右侧**创建**面板中，选择目标集群。

5.  在**应用目录-ack-terway-hubble**页面单击**参数**页签，在`ingress`参数下设置`hosts`，用于后续访问Hubble UI的界面，其他参数请根据实际情况设置。

    **说明：** 以下参数均需要在`ingress`参数下配置。

    |参数|描述|
    |--|--|
    |enabled|是否开启Hubble-UI的Ingress访问。|
    |annotations|自定义Ingress Annotations。|
    |path|自定义Ingress根路径。|
    |hosts|自定义Ingress Hosts。|
    |tls|自定义Ingress TLS配置。|

6.  在右侧**创建**面板中单击**创建**。


## 步骤三：使用ACK Terway Hubble

如果您在ACK Terway Hubble中配置了Ingress域名`hosts`，您可以通过该域名的80端口访问到Hubble UI的界面。访问该域名，您可以在界面上看到以下信息：

**说明：** 如果您使用了非权威解析的域名，例如ingress.local，您需要执行`kubectl -n kube-system get svc nginx-ingress-lb`命令，查看Hubble UI的IP地址，然后在本地hosts进行绑定，将ingress.local指向Ingress Hubble UI的IP地址。

-   在页面上半部分，您可以查看容器集群中各个命名空间中容器组与服务互访的拓扑结构。
-   在页面下半部分，您可以查看当前容器网络中网络流量的来源、目的地、端口信息以及转发状态。
-   如果您设置了网络策略，您可以查看哪些流量是被网络策略丢弃的。

![可观测](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2927473261/p283630.png)

ACK Terway Hubble可以通过kube-system命名空间下hubble-metrics服务暴露一系列网络流量的指标，具体暴露的指标由eni-config中 `cilium_hubble_metrics`字段进行控制。您可以使用Prometheus和ARMS Prometheus采集Metrics数据。具体操作，请参见[开源Prometheus监控](/intl.zh-CN/Kubernetes集群用户指南/可观测性/监控管理/开源Prometheus监控.md)和[阿里云Prometheus监控](/intl.zh-CN/Kubernetes集群用户指南/可观测性/监控管理/阿里云Prometheus监控.md)。

**说明：** 更多ACK Terway Hubble暴露的指标，请参见[hubble-exported-metrics](https://docs.cilium.io/en/v1.8/operations/metrics/#hubble-exported-metrics) 。

|指标大类|指标名|指标字段|说明|
|----|---|----|--|
|drop|hubble\_drop\_total|reason，protocol|丢弃报文|
|tcp|hubble\_tcp\_flags\_total|flag，family|TCP标记|
|flow|hubble\_flows\_processed\_total|type，subtype，verdict|网络流|
|port-distribution|hubble\_port\_distribution\_total|protocol，port|目的端口报文分布|
|icmp|hubble\_icmp\_total|family，type|ICMP报文|

