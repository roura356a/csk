---
keyword: security-inspector
---

# security-inspector

security-inspector组件是实现安全巡检功能的关键组件。本文您介绍security-inspector组件的功能、使用说明和变更记录。

## 组件介绍

security-inspector组件支持对Workload配置进行多维度扫描，帮助您实时了解当前状态下应用是否有安全隐患。security-inspector组件的架构如下图所示。

![security-inspector](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/2814805061/p129654.png)

## 使用说明

security-inspector组件目前支持安全的配置巡检功能。

-   security-inspector组件通过支持使用Polaris进行配置巡检，让您可以实时扫描集群中的Workload配置是否存在安全隐患。

    **说明：** Polaris是一款用于扫描集群中Workload配置是否有安全隐患的开源软件。详情请参见[Polaris](https://github.com/FairwindsOps/polaris)。

-   security-inspector组件可以对Workload配置进行多维度扫描，并可以在巡检报告中查看巡检扫描结果，包括健康检查、镜像、网络、资源、安全等扫描信息。让您实时了解当前状态下运行应用的配置是否有安全隐患，并提供相应的安全修复建议方便进行相应的加固。详情请参见[使用配置巡检检查集群Workload安全隐患](/cn.zh-CN/Kubernetes集群用户指南/安全/容器应用安全/使用配置巡检检查集群Workload安全隐患.md)。

## 变更记录

**2021年09月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.6.0.4-gc12ad66-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/security-inspector:v0.6.0.4-gc12ad66-aliyun|2021年09月20日|-   支持扫描CIS Kubernetes V1.20 Benchmark v1.0.0基线。更多信息，请参见[使用security-inspector组件实现集群安全CIS基线检查](/cn.zh-CN/Kubernetes集群用户指南/安全/基础设施安全/使用security-inspector组件实现集群安全CIS基线检查.md)。
-   优化capabilitiesAdded检查项，检查capabilities时不区分大小写。更多信息，请参见[使用配置巡检检查集群Workload安全隐患](/cn.zh-CN/Kubernetes集群用户指南/安全/容器应用安全/使用配置巡检检查集群Workload安全隐患.md)。

|此次升级不会对业务造成影响。|

**2021年06月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.5.0.2-g5e33765-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/security-inspector:v0.5.0.2-g5e33765-aliyun|2021年06月24日|解决多个集群使用同一个SLS Project时会出现报表数据显示异常的问题。|此次升级不会对业务造成影响。|

**2021年03月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.4.0.0-g541eb31-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/security-inspector:v0.4.0.0-g541eb31-aliyun|2021年03月15日|-   支持CIS Kubernetes基线检查。更多信息，请参见[使用security-inspector组件实现集群安全CIS基线检查](/cn.zh-CN/Kubernetes集群用户指南/安全/基础设施安全/使用security-inspector组件实现集群安全CIS基线检查.md)。
-   新增以下Kubernetes事件（当您触发扫描时，可以在事件中心看到相应事件）：
    -   SecurityInspectorConfigAuditStart：开始执行配置巡检。
    -   SecurityInspectorConfigAuditFinished：配置巡检完成。
    -   SecurityInspectorConfigAuditHighRiskFound：配置巡检完成后发现高风险配置。
    -   SecurityInspectorBenchmarkStart：开始执行基线检查。
    -   SecurityInspectorBenchmarkFinished：执行基线检查完成。
    -   SecurityInspectorBenchmarkFailedCheckFound：执行基线检查完成后发现未通过的计分检查项。

|此次升级不会对业务造成影响。|

**2021年01月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.3.0.2-gcb49252-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/security-inspector:v0.3.0.2-gcb49252-aliyun|2021年01月05日|支持通过扫描匿名用户访问权限配置找出存在安全隐患的RBAC（Role-based access control）权限配置项。|此次升级不会对业务造成影响。|

**2020年12月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.2.0.22-gd1fbaff-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/security-inspector:v0.2.0.22-gd1fbaff-aliyun|2020年12月16日|-   支持以CRD（Custom Resource Definitions）方式存储最新巡检结果。
-   支持启用或禁用指定检查项。
-   支持配置工作负载白名单。

|此次升级不会对业务造成影响。|

**2020年07月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v0.1.0.3-g69f71f6-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/security-inspector:v0.1.0.3-g69f71f6-aliyun|2020年07月06日|支持手动触发配置巡检任务，对集群中的Workload进行检查并输出相应的巡检报告。|此次升级不会对业务造成影响。|

