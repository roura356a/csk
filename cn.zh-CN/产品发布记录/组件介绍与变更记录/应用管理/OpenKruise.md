---
keyword: [OpenKruise组件, 变更记录]
---

# OpenKruise

OpenKruise是基于Kubernetes的一个标准扩展组件，可以配合原生Kubernetes使用，高效管理应用容器、Sidecar容器及镜像分发。本文为您介绍OpenKruise组件的组件介绍、使用说明及变更记录。

## 组件介绍

OpenKruise是阿里云开源的云原生应用自动化引擎，也是阿里巴巴经济体上云全面使用的部署基座，已正式加入CNCF Sandbox。OpenKruise包含了多种自定义Workload，用于无状态应用、有状态应用、Sidecar容器、Daemon应用等部署管理，提供了原地升级、灰度发布、流式发布、发布优先级等策略。

## 使用说明

关于OpenKruise组件的使用说明，请参见[使用OpenKruise组件部署云原生应用](/cn.zh-CN/Kubernetes集群用户指南/发布/使用OpenKruise部署云原生应用.md)。

## 变更记录

**2021年5月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.9.0|registry.cn-hangzhou.aliyuncs.com/acs/kruise-manager:v0.9.0|2021年05月20日|新增：-   支持Pod容器重启及重建。
-   支持级联删除防护。
-   CloneSet支持Pod删除优先级、镜像预热及高效回滚等能力。
-   SidecarSet支持Mesh容器热升级。

|无|

