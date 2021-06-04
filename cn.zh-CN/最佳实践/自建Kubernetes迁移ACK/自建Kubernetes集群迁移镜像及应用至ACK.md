# 自建Kubernetes集群迁移镜像及应用至ACK

本文介绍将自建Kubernetes集群迁移镜像及应用至阿里云容器服务ACK集群的场景描述、方案优势、解决问题、架构图及操作参考链接。

## 场景描述

本最佳实践构建以下场景：

-   以河源ECS构建Harbor仓库，模拟IDC的镜像仓库服务。
-   以河源ECS构建Registry仓库，模拟IDC的镜像仓库服务。
-   以河源地域模的ECS搭建K8s集群，模拟线下IDC的K8s环境。
-   使用velero对云上的K8s应用进行定期备份，并存至OSS上，确保应用数据不丢失。

## 方案优势

随着技术的更新迭代，自建的K8s相关的容器镜像服务、集群管理、稳定性保障让企业IT人员感觉到压力，所以上云可以将底层的IaaS基础设施和K8s的基础PaaS能力交给阿里云来管理。

## 解决问题

平滑、便捷的迁移自建的K8s镜像和应用至阿里云容器服务ACK。

## 架构图

![迁移镜像应用架构图](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4655872261/p280372.png)

## 参考链接

关于如何将自建Kubernetes迁移镜像及应用至阿里云ACK，请参见[自建Kubernetes迁移镜像及应用至阿里云ACK](https://bp.aliyun.com/detail/177)。

