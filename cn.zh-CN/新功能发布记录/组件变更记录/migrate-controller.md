---
keyword: [migrate-controller, Migrate Controller组件变更记录]
---

# migrate-controller

本文介绍Migrate Controller（migrate-controller）组件信息及变更记录。

## 组件介绍

Migrate Controller是阿里云基于开源项目Velero开发的一个Kubernetes应用迁移的组件。有关如何使用Migrate Controller的具体步骤，请参见[安装和使用Migrate Controller](/cn.zh-CN/Kubernetes集群用户指南/多云混合云管理/组件管理/安装和使用Migrate Controller.md)。

Velero是一个云原生的集群应用备份、恢复和迁移工具。Velero采用GO语言编写，可以安全地备份、恢复和迁移Kubernetes集群中的应用及其持久化存储卷。有关Velero源码项目地址，请参见[velero](https://github.com/vmware-tanzu/velero)。有关Velero Plugin for Alibaba Cloud源码项目地址，请参见[velero-plugin](https://github.com/AliyunContainerService/velero-plugin)。

**说明：** Velero的运行环境要求Kubernetes集群版本大于v1.7。

## 变更记录

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v1.0.1.1-30d319f-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/velero-installer:v1.0.1.1-30d319f-aliyun|2020年9月18日|-   支持Kubernetes应用编排及其PV数据的备份和恢复。
-   支持Kubernetes应用编排及其PV数据的迁移。
-   支持定时备份。 |

**相关文档**  


[安装和使用Migrate Controller](/cn.zh-CN/Kubernetes集群用户指南/多云混合云管理/组件管理/安装和使用Migrate Controller.md)

