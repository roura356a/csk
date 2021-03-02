---
keyword: [CoreDNS, 服务发现, DNS]
---

# 基于DNS的服务发现概述

阿里云容器服务Kubernetes版ACK部署了CoreDNS作为集群内的DNS服务器，为集群内的工作负载提供域名解析服务。本文主要介绍CoreDNS的概念、作用，以及CoreDNS作为集群内的DNS服务器的域名解析原理。

## CoreDNS介绍

CoreDNS是Kubernetes集群中负责DNS解析的组件，能够支持解析集群内部自定义服务域名和集群外部域名。CoreDNS具备丰富的插件集，在集群层面支持自建DNS、自定义hosts、CNAME、rewrite等需求。与Kubernetes一样，CoreDNS项目由 [CNCF](https://cncf.io/) 托管。关于CoreDNS的更多信息，请参见[CoreDNS: DNS and Service Discovery](https://coredns.io/)。

ACK集群使用CoreDNS负责集群的服务发现，您可以根据不同使用场景配置CoreDNS及使用CoreDNS提升集群DNS QPS性能：

-   关于ACK集群CoreDNS的详细配置说明，请参见[集群CoreDNS配置](/intl.zh-CN/Kubernetes集群用户指南/网络管理/服务发现DNS/ACK集群内DNS原理和配置说明.md)。
-   关于如何使用CoreDNS提高ACK集群整体DNS查询性能，请参见[优化集群DNS性能](/intl.zh-CN/Kubernetes集群用户指南/网络管理/服务发现DNS/优化集群DNS性能.md)。

**说明：**

您还可以在ACK集群中部署Node Local DNS提升服务发现的稳定性和性能，Node Local DNS通过在集群节点上作为DaemonSet运行DNS缓存代理来提高集群DNS性能。关于如何在集群中部署Node Local DNS的具体步骤，请参见[在ACK集群中部署Node Local DNS]()。

## CoreDNS版本与集群Kubernetes版本关系

ACK采用CoreDNS版本与集群Kubernetes版本绑定的策略。只有ACK集群升级时才会升级CoreDNS版本。CoreDNS版本与集群Kubernetes版本对照关系如下表。

**说明：** 通过在**容器组**中查看名为coredns-xxx的Pod的镜像版本，可以查看CoreDNS当前的版本。具体操作，请参见[查看容器组（Pod）](/intl.zh-CN/Kubernetes集群用户指南/应用管理/查看容器组（Pod）.md)。

|关系项|版本|
|---|--|
|Kubernetes|1.12|1.14|1.16|1.18|
|CoreDNS|1.2.6|1.3.1|1.6.2|1.6.7|

## ACK集群内DNS域名解析原理

ACK集群中kubelet的启动参数有`--cluster-dns=<dns-service-ip>`和`--cluster-domain=<default-local-domain>`，这两个参数分别被用来设置集群DNS服务器的IP地址和主域名后缀。

ACK集群默认部署了一套CoreDNS工作负载，并通过**kube-dns**的服务名暴露DNS服务。ACK部署的CoreDNS工作负载后端是两个名为**coredns**的Pod，集群会根据Pod内的配置，将域名请求发往集群DNS服务器获取结果。Pod内的DNS域名解析配置文件为/etc/resolv.conf，文件内容如下。

```
nameserver 172.xx.x.xx
search kube-system.svc.cluster.local svc.cluster.local cluster.local
options ndots:5
```

![kube-dns（CoreDNS）域名解析图](../images/p232986.png "DNS解析原理示例图")

|序号|描述|
|--|--|
|①|业务Pod（Pod Client）试图访问Nginx服务（Service Nginx）时，先会请求本地DNS配置文件（/etc/resolv.conf）中指向的DNS服务器（nameserver 172.21.0.10，即Service kube-dns）获取服务IP地址，得到解析结果为172.21.0.30的IP地址。|
|②|业务Pod（Pod Client）再直接发起往该IP地址的请求，请求最终经过Nginx服务（Service Nginx）转发到达后端的Nginx容器（Pod Nginx-1和Pod Nginx-2）上。|

关于集群DNS域名解析原理的详细说明，请参见[集群DNS域名解析原理](/intl.zh-CN/Kubernetes集群用户指南/网络管理/服务发现DNS/ACK集群内DNS原理和配置说明.md)。

**相关文档**  


[ACK集群内DNS原理和配置说明](/intl.zh-CN/Kubernetes集群用户指南/网络管理/服务发现DNS/ACK集群内DNS原理和配置说明.md)

[优化集群DNS性能](/intl.zh-CN/Kubernetes集群用户指南/网络管理/服务发现DNS/优化集群DNS性能.md)

[在ACK集群中部署Node Local DNS]()

