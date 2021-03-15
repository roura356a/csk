---
keyword: [security-inspector组件, 变更记录, 动态]
---

# security-inspector组件变更记录

本文为您介绍security-inspector组件相关内容的最新动态。

## 2021年03月

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v0.4.0.0-g541eb31-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/security-inspector:v0.4.0.0-g541eb31-aliyun|2021年03月15日|新增：-   支持CIS Kubernetes基线检查。更多信息，请参见[使用security-inspector组件实现集群安全CIS基线检查](/cn.zh-CN/Kubernetes集群用户指南/安全/使用security-inspector组件实现集群安全CIS基线检查.md)。
-   新增以下Kubernetes事件（当您触发扫描时，可以在事件中心看到相应事件）：
    -   SecurityInspectorConfigAuditStart：开始执行配置巡检。
    -   SecurityInspectorConfigAuditFinished：配置巡检完成。
    -   SecurityInspectorConfigAuditHighRiskFound：配置巡检完成后发现高风险配置。
    -   SecurityInspectorBenchmarkStart：开始执行基线检查。
    -   SecurityInspectorBenchmarkFinished：执行基线检查完成。
    -   SecurityInspectorBenchmarkFailedCheckFound：执行基线检查完成后发现未通过的计分检查项。 |

## 2021年01月

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v0.3.0.2-gcb49252-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/security-inspector:v0.3.0.2-gcb49252-aliyun|2021年01月05日|新增：支持通过扫描匿名用户访问权限配置找出存在安全隐患的RBAC（Role-based access control）权限配置项。|

## 2020年12月

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v0.2.0.22-gd1fbaff-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/security-inspector:v0.2.0.22-gd1fbaff-aliyun|2020年12月16日|新增：-   支持以CRD（Custom Resource Definitions）方式存储最新巡检结果。
-   支持启用或禁用指定检查项。
-   支持配置工作负载白名单。 |

## 2020年07月

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v0.1.0.3-g69f71f6-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/security-inspector:v0.1.0.3-g69f71f6-aliyun|2020年07月06日|新增：支持手动触发配置巡检任务，对集群中的workload进行检查并输出相应的巡检报告。|

**相关文档**  


[security-inspector组件介绍](/cn.zh-CN/新功能发布记录/组件介绍与变更记录/security-inspector/security-inspector组件介绍.md)

