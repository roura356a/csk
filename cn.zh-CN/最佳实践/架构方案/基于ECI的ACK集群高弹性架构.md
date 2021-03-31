---
keyword: [弹性容器实例ECI, 高弹性架构]
---

# 基于ECI的ACK集群高弹性架构

本文介绍基于阿里云弹性容器实例ECI的阿里云容器服务Kubernetes版（ACK）集群高弹性架构的场景描述、解决问题、架构图及操作参考链接。

## 场景描述

您原有的业务部署在ACK集群上并在ACK集群上运行常规业务，当业务突发波动（如秒杀活动）时，让突增的业务运行在ECI实例上，随着业务波动动态，创建或释放ECI实例，达到成本的最优控制。同时，业务方无需管理节点和容量规划，全自动实现容器无限弹性扩容。

## 解决问题

-   长线类业务，例如，业务生产系统。
-   明显的业务弹性诉求，例如，爬虫、秒杀。
-   离线任务。

## 架构图

![基于ECI的ACK集群高弹性架构图](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4563659951/p82645.png)

## 参考链接

有关基于ECI的ACK集群高弹性架构的详情，请参见[基于ECI的ACK集群高弹性架构最佳实践](https://www.aliyun.com/acts/best-practice/preview?id=52147)。

