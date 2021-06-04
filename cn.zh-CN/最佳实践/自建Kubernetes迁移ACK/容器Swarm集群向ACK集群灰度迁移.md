# 容器Swarm集群向ACK集群灰度迁移

本文介绍将容器Swarm集群向ACK集群灰度迁移的场景描述、解决问题、架构图及操作参考链接。

## 场景描述

随着K8s生态的完善，越来越多的客户需要从Swarm集群迁移向ACK集群。

## 解决问题

-   迁移过程中维持业务的延续性
-   迁移过程业务高可用
-   迁移过程可灰度
-   迁移过程可回滚
-   迁移进度可把控

## 架构图

方案说明：

-   容器集群部署，左侧Swarm集群，右侧K8s集群。
-   SLB做应用高可用和应用扩展。
-   DNS结合全局流量控制，权重和后端健康检查。

![架构图_Swarm迁移](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3765872261/p280417.png)

## 参考链接

关于如何将容器Swarm集群向ACK集群灰度迁移，请参见[容器Swarm集群向ACK集群灰度迁移](https://bp.aliyun.com/detail/27)。

