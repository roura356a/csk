---
keyword: [Kube API Serve, API服务器]
---

# Kube API Server

Kube API Server是Kubernetes集群的总线和入口网关。本文介绍Kube API Server组件信息、使用说明和变更记录。

## 组件介绍

Kube API Server用于验证并配置API对象的数据，这些对象包括Pods、Services、ReplicationControllers等。 Kube API Server为REST操作提供服务，并为集群的共享状态提供接口，所有其他组件都通过该接口进行交互。

## 使用说明

Kube API Server组件默认安装，无需配置即可使用。

## 变更记录

Kube API Server组件跟随Kubernetes版本升级，详细介绍，请参见[Kubernetes版本发布概览](/intl.zh-CN/产品发布记录/Kubernetes版本发布说明/Kubernetes版本发布概览.md)。

