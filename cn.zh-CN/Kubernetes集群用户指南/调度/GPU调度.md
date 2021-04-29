---
keyword: [GPU调度, 默认GPU调度, 共享GPU调度, GPU拓扑感知调度]
---

# GPU调度

本文介绍调度GPU资源的不同方法，包括使用Kubernets默认GPU调度、提升GPU资源使用率的共享GPU调度、以及加速任务执行的GPU拓扑感知调度。

## 普通GPU调度

申请Kubernetes GPU集群后，通过运行TensorFlow的GPU实验环境，关于如何使用Kubernetes默认调度器独占GPU，请参见[使用Kubernetes默认GPU调度](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/使用Kubernetes默认GPU调度.md)

您也可通过使用GPU节点标签自定义调度，具体操作，请参见[使用节点标签自定义GPU调度](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/使用节点标签自定义GPU调度.md)

## 共享GPU调度

阿里云容器服务Kubernetes版ACK（Container Service for Kubernetes）开源了GPU共享调度之后，您能在阿里云、AWS、GCE和自己数据中心的容器集群上通过GPU共享调度框架实现多个容器运行在同一个GPU设备上的目标。ACK开源GPU共享调度降低了使用GPU的经济成本。通过隔离，限制运行在同一个GPU上的多个容器能够按照自己申请的资源使用量运行，避免因为其资源用量超标影响同一个GPU上的其他容器的正常工作，同时也使更小颗粒度的使用GPU提供了可能。想进一步了解：

关于什么是共享GPU调度，请参见[共享GPU概述](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/共享GPU调度/共享GPU概述.md)。

关于如何开启共享GPU，请参见[安装共享GPU组件](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/共享GPU调度/安装共享GPU组件.md)；关于如何关闭共享GPU显存隔离能力，请参见[关闭共享GPU显存隔离能力](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/共享GPU调度/关闭共享GPU显存隔离能力.md)。

关于如何使用共享GPU调度，请参见[运行共享GPU示例](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/共享GPU调度/运行共享GPU示例.md)、[监控和隔离GPU资源](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/共享GPU调度/监控和隔离GPU资源.md)、[基于节点池管理共享GPU](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/共享GPU调度/基于节点池管理共享GPU.md)。

## GPU拓扑感知调度

Kubernetes对节点的GPU拓扑信息不感知，调度过程中对GPU的选择比较随机，选择不同的GPU组合训练速度会存在较大的差异。基于以上问题，ACK基于Scheduling Framework机制，实现GPU拓扑感知调度，在节点的GPU组合中选择具有最优训练速度的组合。关于如何使用GPU拓扑感知调度，请参见以下文档：

-   [GPU拓扑感知调度概述](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/GPU调度/GPU拓扑感知调度/GPU拓扑感知调度概述.md)
-   [安装GPU拓扑感知组件](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/GPU调度/GPU拓扑感知调度/安装GPU拓扑感知组件.md)
-   [Tensorflow分布式训练使用GPU拓扑感知调度](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/GPU调度/GPU拓扑感知调度/Tensorflow分布式训练使用GPU拓扑感知调度.md)
-   [Pytorch分布式训练使用GPU拓扑感知调度](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/GPU调度/GPU拓扑感知调度/Pytorch分布式训练使用GPU拓扑感知调度.md)

