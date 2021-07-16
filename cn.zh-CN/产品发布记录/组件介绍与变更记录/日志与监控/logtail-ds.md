---
keyword: [logtail-ds组件介绍, logtail-ds使用说明, logtail-ds变更记录]
---

# logtail-ds

logtail-ds组件是由阿里云日志服务提供，用于采集Kubernetes日志的Agent。本文介绍logtail-ds组件信息、使用说明和变更记录。

## 组件介绍

logtail-ds日志组件提供高性能、低资源消耗的日志采集功能，默认安装在kube-system命名空间下。在安装logtail-ds组件过程中自动完成以下操作：

1.  创建aliyunlogconfigs CRD（Custom Resource Definition），用于向K8s系统注册CRD。
2.  部署alibaba-log-controller的Deployment，负责管理CRD。
3.  以DaemonSet模式安装Logtail，负责采集数据。

logtail-ds支持动态过滤需要采集的容器，支持标准输出、文件、Syslog等多种采集方式，支持多种日志解析方式和配置方式。关于日志采集功能的更多信息，请参见[Logtail采集概述](/cn.zh-CN/数据采集/Logtail采集/简介/Logtail简介.md)。

## 使用说明

有关日志服务logtail-ds组件的使用说明，请参见[通过日志服务采集Kubernetes容器日志](/cn.zh-CN/Kubernetes集群用户指南/可观测性/日志管理/通过日志服务采集Kubernetes容器日志.md)。

## 变更记录

有关日志服务logtail-ds组件的变更记录，请参见[logtail-ds发布历史](/cn.zh-CN/数据采集/Logtail采集/Logtail发布历史.md)。

