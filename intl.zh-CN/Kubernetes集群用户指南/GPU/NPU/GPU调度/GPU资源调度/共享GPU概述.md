---
keyword: [共享GPU, 共享GPU概述, 共享, 隔离]
---

# 共享GPU概述

本文为您介绍阿里云共享GPU方案，帮助您了解和更好地使用共享GPU的能力。

## 背景介绍

阿里云容器服务Kubernetes版ACK（Container Service for Kubernetes）开源了GPU共享调度之后，您能在阿里云、AWS、GCE和自己数据中心的容器集群上通过GPU共享调度框架实现多个容器运行在同一个GPU设备上的目标。ACK开源GPU共享调度降低了使用GPU的经济成本，但是如何能在节省经济成本同时也能让GPU上的容器运行更稳定呢？

隔离是一个关键的需求。如何限制运行在同一个GPU上的多个容器能够按照自己申请的资源使用量运行，避免因为其资源用量超标影响同一个GPU上的其他容器的正常工作，对此业界也做了很多探索。NVIDIA vGPU、MPS，和vCUDA方案，都为更小颗粒度的使用GPU提供了可能。

基于以上的需求，阿里云容器服务团队提供共享GPU方案，既能够实现一张卡供多个任务使用，同时也能够实现单张卡上对各应用申请的显存实现隔离以及卡的算力分割。

## 功能介绍

阿里云提供的共享GPU方案通过自主研发的宿主机内核驱动， 实现对NVIDIA GPU的底层nv驱动更有效的利用。共享GPU功能如下：

-   更加开放：适配开源标准的Kubernetes和NVIDIA Docker方案。
-   更加简单：优秀的用户体验。AI应用无需重编译，无需构建新的容器镜像进行CUDA库替换。
-   更加稳定：针对NVIDIA设备的底层操作更加稳定和收敛，而CUDA层的API变化多端，同时一些Cudnn非开放的API也不容易捕获。
-   完整隔离：同时支持GPU的显存和算力隔离。

阿里云提供的共享GPU方案是一套低成本、可靠、用户友好的规模化GPU调度和隔离方案，欢迎使用。

**说明：** 在不同集群上安装共享GPU组件，组件能力不同，安装的操作步骤也不同：

-   基础版：在专有版GPU集群安装共享GPU组件，具体操作，请参见：
    -   [安装共享GPU组件](/intl.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/GPU资源调度/共享GPU调度/安装共享GPU组件.md)
    -   [运行共享GPU示例](/intl.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/GPU资源调度/共享GPU调度/运行共享GPU示例.md)
    -   [监控和隔离GPU资源](/intl.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/GPU资源调度/共享GPU调度/监控和隔离GPU资源.md)
-   专业版：在ACK Pro版集群安装共享GPU组件，具体操作，请参见：
    -   [安装并使用共享GPU组件和资源工具](/intl.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/GPU资源调度/共享GPU专业版/安装并使用共享GPU组件和资源工具.md)
    -   [运行共享GPU示例](/intl.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/GPU资源调度/共享GPU专业版/运行共享GPU示例.md)
    -   [通过共享GPU实现多卡共享策略](/intl.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/GPU资源调度/共享GPU专业版/通过共享GPU实现多卡共享策略.md)

