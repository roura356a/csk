---
keyword: [共享GPU, 专业版]
---

# 共享GPU调度专业版

本文为您介绍共享GPU调度专业版的相关概念，如何开启共享GPU调度专业版以及如何使用共享GPU调度专业版，帮助您了解和更好地使用共享GPU调度专业版。

阿里云容器服务Kubernetes版ACK（Container Service for Kubernetes）开源了GPU共享调度之后，您能在阿里云、AWS、GCE和自己数据中心的容器集群上通过GPU共享调度框架实现多个容器运行在同一个GPU设备上的目标。ACK开源GPU共享调度降低了使用GPU的经济成本。通过隔离，限制运行在同一个GPU上的多个容器能够按照自己申请的资源使用量运行，避免因为其资源用量超标影响同一个GPU上的其他容器的正常工作，同时也使更小颗粒度的使用GPU提供了可能。想进一步了解：

-   关于什么是共享GPU调度，请参见[共享GPU调度概述](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/共享GPU调度/共享GPU调度概述.md)。
-   关于如何开启共享GPU调度专业版，请参见[安装并使用共享GPU组件和资源工具](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/共享GPU调度/专业版/安装并使用共享GPU组件和资源工具.md)。
-   关于如何使用共享GPU调度专业版，请参见[运行共享GPU示例](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/共享GPU调度/专业版/运行共享GPU示例.md)、[通过共享GPU实现多卡共享策略](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/共享GPU调度/专业版/通过共享GPU实现多卡共享策略.md)、[基于节点池管理共享GPU](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/共享GPU调度/专业版/基于节点池管理共享GPU.md)。
-   若ACK专有版集群已安装共享GPU调度基础版，在迁移至ACK Pro托管版集群后，需要将共享GPU调度基础版升级为共享GPU调度专业版。具体操作，请参见[在ACK Pro版集群中将共享GPU基础版升级为共享GPU专业版](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/共享GPU调度/专业版/在ACK Pro版集群中将共享GPU基础版升级为共享GPU专业版.md)。

