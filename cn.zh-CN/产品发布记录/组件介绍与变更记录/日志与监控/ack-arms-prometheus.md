---
keyword: [ack-arms-prometheus, ARMS Prometheus]
---

# ack-arms-prometheus

ack-arms-prometheus组件是ARMS Prometheus在ACK集群中的监控组件。本文介绍ack-arms-prometheus组件信息、使用说明和变更记录。

## 组件介绍

ack-arms-prometheus是阿里云容器服务Kubernetes版提供对接ARMS Prometheus的监控组件。当在ACK集群开启Prometheus 监控功能时，需要安装此组件。此组件负责上报采集到的监控数据至ARMS Prometheus，ARMS Prometheus根据上报的监控数据形成数据链路。您可以在ARMS Prometheus查看监控大盘、数据链路等信息。

## 使用说明

关于ack-arms-prometheus组件的使用，请参考[ARMS Prometheus监控](/cn.zh-CN/Kubernetes集群用户指南/可观测性/监控管理/阿里云Prometheus监控.md)。

## 变更记录

**2021年07月**

|版本号|变更时间|变更内容|变更影响|
|---|----|----|----|
|0.1.8|2021年07月|兼容能力优化。|此次升级不会对业务造成影响。|

**2020年10月**

|版本号|变更时间|变更内容|变更影响|
|---|----|----|----|
|0.1.5|2020年10月|-   支持Kubernetes 1.18集群。
-   支持拉取镜像内网地址。

|此次升级不会对业务造成影响。

建议升级到最新版本。 |

**2020年07月**

|版本号|变更时间|变更内容|变更影响|
|---|----|----|----|
|0.1.4|2020年07月|-   开箱即用的K8s容器监控，包括Pod监控、Node监控和Resource监控等，主要用于监控应用所在的K8s容器运行时。
-   白屏化的组件监控，包括MySQL、Redis、Kafka、ZooKeeper和Nginx等常见的9种组件监控，主要用于监控应用依赖中间件的场景。
-   全托管的Prometheus监控系统，包括Prometheus.yaml采集规则、Grafana大盘和告警系统，可以满足自建Prometheus迁移阿里云的需求场景。

|此次升级不会对业务造成影响。

建议升级到最新版本。 |

**2020年04月**

|版本号|变更时间|变更内容|变更影响|
|---|----|----|----|
|0.1.3|2020年04月|增加Agent资源使用控制。|此次升级不会对业务造成影响。

建议升级到最新版本。 |

**2019年08月**

|版本号|变更时间|变更内容|变更影响|
|---|----|----|----|
|0.1.2|2019年08月|初始发布版本。|此次升级不会对业务造成影响。

建议升级到最新版本。 |

**相关文档**  


[ack-arms-prometheus组件常见问题]()

