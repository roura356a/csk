---
keyword: [ACK弹性裸金属集群, Kubernetes迁移]
---

# 自建Kubernetes迁移至ACK弹性裸金属集群

本文介绍将自建Kubernetes集群迁移至ACK弹性裸金属集群的场景描述、解决问题、架构图及操作参考链接。

## 场景描述

在微服务化改造之后，企业在享受Kubernetes带来应用管理的便利的同时，存在硬件性能不足、本地扩展性差、容器容灾难、Kubernetes管理复杂等问题。

## 解决问题

-   增强Kubernetes扩展性：如何通过弹性裸金属服务器的强劲硬件性能，实现云端资源急速扩展，从容应对应用访问压力大的问题。
-   如何简化云端Kubernetes运维，通过ACK实现敏捷开发和部署落地，加速企业业务迭代。
-   如何综合考虑迁移和容灾，如何整合云上和云下容器资源实现遇到故障时可以通过健康检查实现自动容灾。
-   如何不改应用上云，实现应用上云数据库连接零修改。
-   数据库上云及回退，如何实现上云回退。

## 架构图

![自建k8s迁移至ack弹性裸金属集群](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1763659951/p82636.png)

## 参考链接

有关如何将自建Kubernetes集群迁移至ACK弹性裸金属集群，请参见[自建Kubernetes集群迁移ACK弹性裸金属集群最佳实践](https://www.aliyun.com/acts/best-practice/preview?id=52152)。

