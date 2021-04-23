---
keyword: [CoreDNS, DNS服务插件]
---

# CoreDNS

本文为您介绍CoreDNS相关内容的最新动态。

## 组件介绍

CoreDNS是ACK集群和边缘集群中默认采用的DNS服务发现插件，其遵循Kubernetes DNS-Based Service Discovery规范。更多信息，请参见[Kubernetes DNS-Based Service Discovery](https://github.com/kubernetes/dns/blob/master/docs/specification.md)。CoreDNS提供了Kubernetes集群内部服务的域名解析能力。CoreDNS版本与Kubernetes 版本存在版本对应关系。在创建和升级您的集群时，将会遵循CoreDNS version in Kubernetes中版本对应关系为您安装兼容版本的CoreDNS。关于版本对应关系的更多信息，请参见[CoreDNS version in Kubernetes](https://github.com/coredns/deployment/blob/master/kubernetes/CoreDNS-k8s_version.md)。

## 使用说明

关于CoreDNS的功能和使用，请参见[ACK集群内DNS原理和配置说明](/cn.zh-CN/Kubernetes集群用户指南/网络/服务发现DNS/ACK集群内DNS原理和配置说明.md)。

## 变更记录

**2021年04月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|1.7.0（该版本适用于非边缘集群。）|registry.\{\{.Region\}\}.aliyuncs.com/acs/coredns:1.7.0|2021年04月|-   删除了对已弃用插件Upstream的兼容。如果Corefile配置项中包含Upstream插件，Upstream插件会在升级过程中被安全、自动地删除。
-   更新指标（Metrics）名称。如果您的监控系统依赖于CoreDNS指标，请注意修改。更多信息，请参见[Metric Changes](https://coredns.io/2020/06/15/coredns-1.7.0-release/#metric-changes)。

|无|

**2021年01月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|1.6.7.edge（该版本适用于边缘集群。）|registry.\{\{.Region\}\}.aliyuncs.com/acs/coredns:1.6.7.edge|2021年01月|基于社区1.6.7版本构建。更多信息，请参见[CoreDNS-1.6.7 Release](https://coredns.io/2020/01/28/coredns-1.6.7-release/)。|无|
|1.6.7（该版本适用于非边缘集群。）|registry.\{\{.Region\}\}.aliyuncs.com/acs/coredns:1.6.7|2021年01月|基于社区1.6.7版本构建。更多信息，请参见[CoreDNS-1.6.7 Release](https://coredns.io/2020/01/28/coredns-1.6.7-release/)。|无|

