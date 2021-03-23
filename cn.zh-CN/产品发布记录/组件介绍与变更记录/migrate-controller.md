---
keyword: [migrate-controller, 发布记录]
---

# migrate-controller

migrate-controller是基于开源项目Velero开发的一个Kubernetes应用迁移的组件。本文为您介绍migrate-controller组件相关内容的最新动态。

## 使用说明

有关migrate-controller组件使用的详情，请参见[安装Migrate Controller](/cn.zh-CN/Kubernetes集群用户指南/多云混合云/组件管理/安装Migrate Controller.md)。

## 2020年12月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.1.2-h-e616322-aliyun|registry.\{\{.Region\}\}.aliyuncs.com/acs/velero-installer:v1.0.1.2-h-e616322-aliyun|2020年12月24日|ACK注册集群的Velero更新到社区最新的1.5.2版本。|预计影响正在执行备份的任务，该任务可能会被中断。|
|v1.0.1.2-a-e616322-aliyun|registry-vpc.\{\{.Region\}\}.aliyuncs.com/acs/velero-installer:v1.0.1.2-h-e616322-aliyun|2020年12月30日|ACK托管版集群、ACK专有版集群的Velero更新到社区最新的1.5.2版本。|预计影响正在执行备份的任务，该任务可能会被中断。|

## 2020年09月

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.1.1-30d319f-aliyun|registry.\{\{.Region\}\}.aliyuncs.com/acs/velero-installer:v1.0.1.1-30d319f-aliyun|2020年09月21日|支持ACK注册集群、ACK托管版集群应用的迁移、备份和恢复。|首次上线。|

**相关文档**  


[安装Migrate Controller](/cn.zh-CN/Kubernetes集群用户指南/多云混合云/组件管理/安装Migrate Controller.md)

[使用Migrate Controller备份和恢复应用](/cn.zh-CN/Kubernetes集群用户指南/多云混合云/组件管理/使用Migrate Controller备份和恢复应用.md)

[使用Migrate Controller跨集群迁移应用](/cn.zh-CN/Kubernetes集群用户指南/多云混合云/组件管理/使用Migrate Controller跨集群迁移应用.md)

