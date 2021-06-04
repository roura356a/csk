---
keyword: [混合云IDC自有K8s, 自有K8s弹性使用ECI]
---

# 混合云IDC自有K8s弹性使用ECI

本文介绍混合云IDC自有K8s弹性使用ECI的场景描述、解决问题、架构图及操作参考链接。

## 场景描述

本文介绍线下IDC与云端通过专线构建混合云架构，自有K8s利用虚拟节点弹性调用ECI承载业务高峰期资源需求的最佳实践。

## 解决问题

-   在混合云环境下，将自有K8s集群注册至ACK，实现云端纳管。
-   纳管K8s集群部署Virtual Node，使集群具备ECI资源调度能力。
-   在以上环境中部署Web及离线作业应用，并使用ECI资源作为弹性资源池满足业务波峰需求。

## 架构图

![BP4](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7268542261/p278983.png)

## 参考链接

有关混合云IDC自有K8s弹性使用ECI的详情，请参见[混合云IDC自有K8s弹性使用ECI](https://bp.aliyun.com/detail/161)。

