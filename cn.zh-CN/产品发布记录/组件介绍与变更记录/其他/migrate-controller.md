---
keyword: [migrate-controller, Migrate Controller组件变更记录]
---

# migrate-controller

migrate-controller是基于开源项目Velero开发的组件，并与阿里云快照、HBR产品深度结合，可以为您提供数据及应用模板的备份、恢复及迁移。本文为您介绍migrate-controller组件信息、使用说明及变更记录。

## 组件介绍

有关如何安装Migrate Controller的具体步骤，请参见[开启集群备份](/cn.zh-CN/Kubernetes集群用户指南/备份中心/开启集群备份.md)。

migrate-controller可以对您的K8s应用、PV数据进行备份。对于支持多种筛选策略备份应用。例如，命名空间级别、标签级别或资源级别等。对于数据支持云盘快照及HBR文件备份，同时可通过转换快照降低恢复时的RTO。

## 使用说明

您可以使用Migrate Controller组件备份、恢复和迁移应用。具体操作，请参见[备份和恢复应用](/cn.zh-CN/Kubernetes集群用户指南/备份中心/备份和恢复应用.md)和[跨集群迁移应用](/cn.zh-CN/Kubernetes集群用户指南/备份中心/跨集群迁移应用.md)。

## 变更记录

**2021年09月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.5.2-d728be2-aliyun|registry-vpc.\{\{.Region\}\}.aliyuncs.com/acs/velero-installer:v1.5.2-d728be2-aliyun|2021年09月10日|-   兼容老版本。
-   升级快照创建方式。
-   升级文件备份为HBR。
-   支持文件转换为云盘快照及NAS快照，便于恢复时降低RTO。
-   升级托管版集群的权限控制策略。
-   不再支持使用Restic备份数据。

|预计影响正在执行备份的任务，该任务可能会被中断。|

**2021年07月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.4.1-f745291-aliyun|registry-vpc.\{\{.Region\}\}.aliyuncs.com/acs/velero-installer:v1.4.1-f745291-aliyun|2021年07月13日|支持根据K8s及CSI版本确定快照特性的启用。|预计影响正在执行备份的任务，该任务可能会被中断。|

**2021年03月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.3.1-3026c10-aliyun|registry-vpc.\{\{.Region\}\}.aliyuncs.com/acs/velero-installer:v1.3.1-3026c10-aliyun|2021年03月31日|升级组件卸载逻辑及组件异常修复。|预计影响正在执行备份的任务，该任务可能会被中断。|
|v1.3.1-665c3ea-aliyun|registry.\{\{.Region\}\}.aliyuncs.com/acs/velero-installer:v1.3.1-665c3ea-aliyun|2021年03月31日|注册集群支持云盘快照。|预计影响正在执行备份的任务，该任务可能会被中断。|
|v1.3.0-9274575-aliyun|registry-vpc.\{\{.Region\}\}.aliyuncs.com/acs/velero-installer:v1.0.1.2-h-e616322-aliyun|2021年03月29日|增加云盘快照模式以支持卷存储备份。|预计影响正在执行备份的任务，该任务可能会被中断。如有升级需求，请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)联系技术支持人员。|

**2020年12月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.1.2-h-e616322-aliyun|registry.\{\{.Region\}\}.aliyuncs.com/acs/velero-installer:v1.0.1.2-h-e616322-aliyun|2020年12月24日|ACK注册集群的Velero更新到社区最新的1.5.2版本。|预计影响正在执行备份的任务，该任务可能会被中断。如有升级需求，请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)联系技术支持人员。|
|v1.0.1.2-a-e616322-aliyun|registry-vpc.\{\{.Region\}\}.aliyuncs.com/acs/velero-installer:v1.0.1.2-h-e616322-aliyun|2020年12月30日|ACK托管版集群、ACK专有版集群的Velero更新到社区最新的1.5.2版本。|预计影响正在执行备份的任务，该任务可能会被中断。如有升级需求，请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)联系技术支持人员。|

**2020年09月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.1.1-30d319f-aliyun|registry.\{\{.Region\}\}.aliyuncs.com/acs/velero-installer:v1.0.1.1-30d319f-aliyun|2020年09月21日|支持ACK注册集群、ACK托管版集群应用的迁移、备份和恢复。|首次上线。|

