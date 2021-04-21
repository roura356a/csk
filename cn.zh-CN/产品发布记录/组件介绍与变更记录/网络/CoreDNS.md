---
keyword: [CoreDNS, DNS服务插件]
---

# CoreDNS

CoreDNS是一个高扩展性的DNS服务插件，可以用来为Kubernetes提供域名解析服务。本文为您介绍CoreDNS相关内容的最新动态。

## 组件介绍

CoreDNS是ACK中默认的DNS插件，为集群内的工作负载提供域名解析服务。您可以通过Daemonset方式部署CoreDNS。CoreDNS具备丰富的插件集，在集群层面支持自建DNS、自定义hosts、CNAME、rewrite等需求。ACK集群中默认部署形态在DNS QPS较高场景下，可能会出现解析压力，导致部分查询失败。使用CoreDNS插件有助于提升集群服务发现的性能。

## 使用说明

关于CoreDNS的功能和使用，请参见[ACK集群内DNS原理和配置说明](/cn.zh-CN/Kubernetes集群用户指南/网络/服务发现DNS/ACK集群内DNS原理和配置说明.md)。

## 变更记录

**2021年01月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|1.6.7.edge（该版本适用于边缘集群。）|registry.\{\{.Region\}\}.aliyuncs.com/acs/coredns:1.6.7.edge|2021年01月|基于社区1.16.7版本构建。更多信息，请参见[CoreDNS-1.6.7 Release](https://coredns.io/2020/01/28/coredns-1.6.7-release/)。|无|

