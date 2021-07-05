---
keyword: [任务调度, GPU调度, 资源利用率]
---

# AI调度

本文介绍AI任务调度和异构资源调度，帮助用户提升集群的资源利用率和作业运行效率。

## 任务调度

**Gang Scheduling**

对于有All-or-Nothing特点的作业，它要求所有的任务在同一时间被调度，如果只是部分任务启动的话，启动的任务将持续等待剩余的任务被调度。在最坏的情况下，所有作业都处于挂起状态，从而导致死锁。为了解决这个问题，阿里云提供了Gang scheduling。更多信息，请参见[Gang scheduling](/intl.zh-CN/Kubernetes集群用户指南/调度/任务调度/Gang scheduling.md)。

**Capacity Scheduling**

当集群中有多个用户时，通过Kubernetes原生的ResourceQuota方式进行固定资源分配，因为不同的用户使用资源的周期和方式不同，会造成集群的整体资源利用率不高。阿里云借鉴Yarn Capacity Scheduling的设计思路，基于Scheduling Framework的扩展机制，在调度侧通过引入弹性配额组实现了Capacity Scheduling功能，在确保用户资源分配的基础上通过资源共享的方式来提升集群的整体资源利用率。更多信息，请参见[Capacity Scheduling](/intl.zh-CN/Kubernetes集群用户指南/调度/任务调度/Capacity Scheduling.md)。

## 异构资源调度

**GPU共享调度**

GPU共享调度除了提供GPU共享调度降低了使用GPU的经济成本，同时为了保障程序运行的稳定性，提供了GPU显存和算力隔离的功能。更多信息，请参见：[共享GPU概述](/intl.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/共享GPU调度/共享GPU概述.md)。 同时在ACK Pro集群中提供了更加丰富的GPU设备分配策略：包括单Pod单GPU卡共享和隔离，常用于支持模型推理场景；单Pod多GPU卡共享和隔离，常用于支持分布式模型训练代码的开发；按GPU卡的Binpack和Spread分配策略，可以针对GPU卡的使用密度和出现坏卡时高可用性的平衡。更多信息，请参见[共享GPU专业版概述](/intl.zh-CN/Kubernetes集群用户指南/ACK Pro集群/GPU资源调度/共享GPU专业版/共享GPU专业版概述.md)。

**拓扑感知调度**

调度器基于节点异构资源的拓扑信息，如GPU卡之间的Nvlink、PcleSwitch等通信方式，或者CPU的NUMA拓扑结构，在集群维度进行最佳的调度选择，提供给AI作业更好的性能。关于GPU拓扑感知调度，请参见[GPU拓扑感知调度概述](/intl.zh-CN/Kubernetes集群用户指南/ACK Pro集群/GPU资源调度/GPU拓扑感知调度/GPU拓扑感知调度概述.md)。关于CPU拓扑感知调度，请参见[CPU拓扑感知调度](/intl.zh-CN/Kubernetes集群用户指南/调度/CPU拓扑感知调度/CPU拓扑感知调度.md)。

