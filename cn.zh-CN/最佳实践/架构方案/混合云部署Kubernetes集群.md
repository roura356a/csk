---
keyword: [混合云, 部署Kubernetes集群]
---

# 混合云部署Kubernetes集群

本文介绍混合云部署Kubernetes集群的场景描述、解决问题、架构图及操作参考链接。

## 场景描述

本方案演示线下IDC与阿里云以混合云的形式部署Kubernetes集群，实现云下云上服务交叉调用。云上资源可以根据业务请求峰谷值做弹性伸缩。使用数据传输服务DTS实现IDC与云上数据库单向同步，将云上数据库作为混合云场景下的冗余和灾备，可以在云上部署可接受一定读延迟的业务应用，缓解IDC机房业务压力。

## 解决问题

-   云上冗余部署和快速弹性伸缩，低成本解决IDC弹性伸缩问题。
-   云上应用可作为接受一定读延迟请求分流后端，降低IDC主服务的压力。
-   容器镜像服务ACR企业版支持多地域同步部署，加速镜像分发。
-   数据传输服务DTS单向同步数据灾备，提升IDC机房数据库高可用。

## 架构图

![混合云部署Kubernetes集群最佳实践](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5563659951/p82700.png)

## 参考链接

有关混合云部署Kubernetes集群的详情，请参见[混合云部署Kubernetes集群最佳实践](https://www.aliyun.com/acts/best-practice/preview?id=125511)。

