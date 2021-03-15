---
keyword: [security-inspector组件, 介绍, 安全巡检功能]
---

# security-inspector组件介绍

security-inspector组件是实现安全巡检功能的关键组件。本文介绍security-inspector组件的架构以及目前所支持的安全巡检功能。

## 架构

security-inspector组件的架构图如下。

![security-inspector](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2814805061/p129654.png)

## 安全巡检功能

security-inspector组件目前支持安全的配置巡检功能。

-   security-inspector组件通过支持使用Polaris进行配置巡检，让您可以实时扫描集群中的workload配置是否存在安全隐患。

    **说明：** Polaris是一款用于扫描集群中workload配置是否有安全隐患的开源软件。详情请参见[Polaris](https://github.com/FairwindsOps/polaris)。

-   security-inspector组件通过对workload配置进行health-check、image策略、network配置、resource、security-capabilities以及安全配置参数等多种维度的扫描，让您实时了解当前状态下运行应用的配置是否有安全隐患，并提供相应的安全修复建议文档方便用户进行相应的加固。详情请参见[使用配置巡检检查集群workload安全隐患](/cn.zh-CN/Kubernetes集群用户指南/安全/安全中心/使用配置巡检检查集群workload安全隐患.md)。

**相关文档**  


[security-inspector组件变更记录](/cn.zh-CN/新功能发布记录/组件介绍与变更记录/security-inspector/security-inspector组件变更记录.md)

