---
keyword: [yurt-app-manager, 边缘容器服务ACK@Edge, 边缘计算]
---

# yurt-app-manager

yurt-app-manager是为ACK@Edge集群提供边缘单元化管理的功能组件。本文为您介绍yurt-app-manager组件，以及使用说明和相关内容的最新动态。

## 组件介绍

-   在边缘计算场景下，边缘节点通常具备很强的区域性、地域性、或者其他逻辑上的分组特性，比如具有相同的CPU架构、运营商或云提供商。
-   相同的应用和镜像，可能需要部署到不同的节点池中。
-   原生Kubernetes Service的后端端点扁平分布在集群中任意节点。因此，跨跃不同分组节点的Service流量，会大概率出现访问不可达、或者访问效率低下的问题。

针对以上场景，ACK@Edge提出了如下图所示解决方案。

![G-1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8964401161/p214171.png)

-   节点单元化：以节点池视角对不同边缘区域下的主机进行统一管理和运维。
-   应用的单元化：使用新的单元化部署模型将用户的工作负载部署在不同的节点池中，业务的实例数、版本都可以按照节点池的维度进行统一管理。
-   流量的单元化：通过配置Service拓扑来限制Service后端Endpoint的被访问范围，例如边缘节点应用只能由相同节点池的节点访问，或者只能由本节点访问。

节点单元化（边缘节点池）和应用的单元化（单元化部署）功能是由yurt-app-manager组件提供。

## 使用说明

yurt-app-manager组件提供两个控制器功能：NodePool和UnitedDeployment，分别实现节点单元化和应用的单元化功能。更多信息，请参见[边缘节点池概述](/intl.zh-CN/边缘容器服务ACK@Edge用户指南/边缘单元化管理/边缘节点池管理/边缘节点池概述.md)和[使用单元化部署应用模型](/intl.zh-CN/边缘容器服务ACK@Edge用户指南/边缘单元化管理/使用单元化部署应用模型.md)。

**说明：** 该组件在创建边缘集群时会默认安装。关于边缘集群升级组件的操作，请参见[升级边缘集群组件](/intl.zh-CN/边缘容器服务ACK@Edge用户指南/边缘托管集群管理/升级边缘集群组件.md)。

## 变更记录

**2021年04月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.10.6-8207677-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/yurt-app-manager:v0.10.6-8207677-aliyun|2021年04月13日|-   支持默认使用Lease选举。
-   UnitedDeployment单元化部署新增Patch字段， 您可以对每个节点池进行自定义的差异化配置，例如：镜像版本等。

|无。|

**2021年01月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.9.11-c2c8cce-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/yurt-app-manager:v0.9.11-c2c8cce-aliyun|2021年01月14日|首次发布yurt-app-manager组件。|无。|

