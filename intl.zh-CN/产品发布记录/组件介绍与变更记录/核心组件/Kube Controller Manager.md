---
keyword: Kube Controller Manager
---

# Kube Controller Manager

Kube Controller Manager是Kubernetes集群内部资源的管理器。本文介绍Kube Controller Manager组件的信息、使用说明和变更记录。

## 组件介绍

Kube Controller Manager是一个守护进程，内嵌随Kubernetes一起发布的核心控制回路。Kube Controller Manager通过API服务器监控集群的状态，确保集群处于预期的工作状态。Kube Controller Manager由负责不同资源的多个控制器构成。目前，Kubernetes自带的控制器包括副本控制器、节点控制器、命名空间控制器和服务账号控制器等。

## 使用说明

Kube Controller Manager组件默认安装，无需配置即可使用。

## 变更记录

Kube Controller Manager组件跟随Kubernetes版本升级，详细介绍，请参见[Kubernetes版本发布概览](/intl.zh-CN/产品发布记录/Kubernetes版本发布说明/Kubernetes版本发布概览.md)。

