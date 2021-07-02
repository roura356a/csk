---
keyword: [CoreDNS, Analyser, 域名解析异常, DNSTAP]
---

# 使用ACK CoreDNS DNSTAP Analyser诊断CoreDNS域名解析异常

ACK集群的ACK CoreDNS DNSTAP Analyser组件，可以从CoreDNS和客户端之间，CoreDNS和上游DNS服务器之间的请求和相应过程中，对所有产生的DNS网络报文进行归类和识别。借助此工具，您可以快速定位域名无法解析的原因。本文介绍如何使用ACK CoreDNS DNSTAP Analyser诊断CoreDNS域名解析异常。

-   [创建ACK Pro版集群](/intl.zh-CN/Kubernetes集群用户指南/ACK Pro集群/创建ACK Pro版集群.md)
-   [通过kubectl管理Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl管理Kubernetes集群.md)

DNSTap是一种DNS报文日志格式协议，可以被用于诊断DNS服务器上异常解析问题。更多信息，请参见[dnstap](https://dnstap.info/)。

## 步骤一：安装ACK CoreDNS DNSTAP Analyser

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

3.  在应用目录页面搜索ack-coredns-dnstap-analyser，找到并单击**ack-coredns-dnstap-analyser**。

4.  在应用目录-ack-coredns-dnstap-analyser页面右侧**创建**面板中，选择目标集群，然后在页面左侧单击**说明**页签并阅读相关信息，然后在**参数**页签中根据需求设置参数。

5.  在页面右侧，单击**创建**。


## 步骤二：配置CoreDNS的DNSTAP插件

CoreDNS中内置了发送DNSTap报文的DNSTAP插件，DNSTAP插件收到和发出的DNS报文可以发送至ACK CoreDNS DNSTAP Analyser。使用ACK CoreDNS DNSTAP Analyser诊断CoreDNS域名解析异常前，您需要先配置CoreDNS的DNSTAP插件。

1.  执行以下命令，获取kube-system命名空间下ACK CoreDNS DNSTAP Analyser服务的ClusterIP地址（例如172.21.0.10）用于配置DNSTAP插件。

    ```
    kubectl -n kube-system get svc dnstap-analyser
    ```

2.  执行以下命令，编辑CoreDNS配置文件。

    ```
    kubectl -n kube-system edit cm coredns -o yaml
    ```

3.  参考以下注释修改CoreDNS的默认配置。

    修改配置的目的是启用DNSTAP插件，并指向地址至CoreDNS DNSTAP Analyser的ClusterIP地址，从而将DNSTAP插件收到和发出的DNS报文发送至ACK CoreDNS DNSTAP Analyser。

    ```
    json      Corefile: |
            .:53 {
                // 省略
                ready
    
                // 添加以下一行，将172.21.0.10替换成获取到的CoreDNS DNSTAP Analyser ClusterIP地址。
                dnstap tcp://172.21.0.10:6000 full
    
                kubernetes cluster.local in-addr.arpa ip6.arpa {
                  // 省略
                }
                // 省略
                reload
                loadbalance
            }
    ```

4.  执行以下命令，查看当前CoreDNS的所有容器组。

    ```
    kubectl -n kube-system get pod | grep coredns
    ```

    预期输出：

    ```
    coredns-7d56l         1/1     Running   0          30m
    coredns-s7m2t         1/1     Running   0          30m
    ```

5.  执行以下命令，持续查看CoreDNS日志。

    ```
    kubectl -n kube-system logs -f --tail=500 coredns-7d56l
    ```

    **说明：** 替换容器组名称coredns-7d56l为步骤3中出现的任意一容器组名称。

    等待约一分钟，观察日志中出现以下输出，并无其它异常时，说明配置修改成功。

    ```
    [INFO] Reloading complete
    ```


通过以上操作您已启用CoreDNS DNSTAP插件，此时CoreDNS会将接收和发送到的DNS报文转发至ACK CoreDNS DNSTAP Analyser进行自动化诊断。

## 步骤三：查看ACK CoreDNS DNSTAP Analyser日志

1.  执行以下命令，查看ACK CoreDNS DNSTAP Analyser的所有容器组。

    ```
    kubectl -n kube-system get pod | grep dnstap-analyser
    ```

    预期输出：

    ```
    dnstap-analyser-bbdf879-gpfkm         1/1     Running   0          30m
    ```

2.  执行以下命令，查看ACK CoreDNS DNSTAP Analyser日志。

    ```
    kubectl -n kube-system logs -f dnstap-analyser-bbdf879-gpfkm
    ```

    **说明：** 替换容器组名称dnstap-analyser-bbdf879-gpfkm为步骤1中出现的容器组名称。


## 步骤四：分析ACK CoreDNS DNSTAP Analyser日志

在ACK CoreDNS DNSTAP Analyser日志中，每一行是一个经过聚合的DNS请求上下文（Session），包含了从客户端、CoreDNS、上游DNS服务器之间的所有请求和响应的报文。DNS请求上下文（Session）包括：

-   Status字段：不同的Status代表不同的场景。

    |上下文状态|描述|
    |-----|--|
    |Succeeded|完整的域名查询请求，域名的响应是正常的（状态为NXDOMAIN、NOERROR）。|
    |Failed|有问题的域名查询请求，域名响应中状态码异常或上游服务器连接超时等情况。|
    |SampleLoss|CoreDNS或Analyser负载较高，导致DNSTAP报文丢失，出现这类状态时，应调整CoreDNS或Analyser副本数，使单个容器的负载下降，提高诊断可信度。|

-   BitMap字段：BitMap的值代表了该请求上下文中截取到的DNSTAP报文的种类，根据截取到的不同种类，得到可能的异常原因。

    |上下文Session BitMap值|含义|
    |------------------|--|
    |1|CoreDNS接收到了客户端的查询请求，但未返回结果给客户端。|
    |3|一次完整的内部域名查询，从客户端发起了请求，CoreDNS本地缓存中或Kubernetes中存在命中该域名的解析结果，将结果返回给了客户端。|
    |5|无法连接到上游服务器，从客户端发起了请求，CoreDNS将请求转发给上游服务器进行查询，上游服务器未返回结果。|
    |15|一次完整的外部域名查询，从客户端发起了请求，CoreDNS将请求转发给上游服务器进行查询，上游服务器完成了查询并返回，CoreDNS又将结果返回给了客户端。|

-   Messages字段：DNS请求上下文Session中Messages字段记录了DNS请求、响应的原始报文的JSON输出，可以根据[dns-parameters-6](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml#dns-parameters-6)对报文内容进行问题诊断。

## （可选）步骤五：通过SLS仪表盘查看异常DNS报文信息

如果您配置了阿里云日志服务SLS的仪表盘，并且在ACK CoreDNS DNSTAP Analyser组件安装时使用了推荐的`env`日志采集配置（参考[步骤4](#step_i60_qp4_61q)中的**说明**页签的内容），您可以在ACK集群对应的SLS Project的Kubernetes CoreDNS DNSTAP报文监控仪表盘中看到异常DNS报文信息。具体操作，请参见[分析和监控CoreDNS日志](/intl.zh-CN/Kubernetes集群用户指南/可观测性/日志管理/分析和监控CoreDNS日志.md)。

**相关文档**  


[基于DNS的服务发现概述](/intl.zh-CN/Kubernetes集群用户指南/网络/服务发现DNS/基于DNS的服务发现概述.md)

[ACK集群内DNS原理和配置说明](/intl.zh-CN/Kubernetes集群用户指南/网络/服务发现DNS/ACK集群内DNS原理和配置说明.md)

