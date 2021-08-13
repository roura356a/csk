# GPU拓扑感知调度

Kubernetes对节点的GPU拓扑信息不感知，调度过程中对GPU的选择比较随机，选择不同的GPU组合训练速度会存在较大的差异。基于以上问题，ACK基于Scheduling Framework机制，实现GPU拓扑感知调度，在节点的GPU组合中选择具有最优训练速度的组合。

关于GPU拓扑感知调度如何使用，请参见以下文档：

-   [GPU拓扑感知调度概述](/intl.zh-CN/Kubernetes集群用户指南/ACK Pro集群/GPU资源调度/GPU拓扑感知调度/GPU拓扑感知调度概述.md)
-   [安装GPU拓扑感知组件](/intl.zh-CN/Kubernetes集群用户指南/ACK Pro集群/GPU资源调度/GPU拓扑感知调度/安装GPU拓扑感知组件.md)
-   [Tensorflow分布式训练使用GPU拓扑感知调度](/intl.zh-CN/Kubernetes集群用户指南/ACK Pro集群/GPU资源调度/GPU拓扑感知调度/Tensorflow分布式训练使用GPU拓扑感知调度.md)
-   [Pytorch分布式训练使用GPU拓扑感知调度](/intl.zh-CN/Kubernetes集群用户指南/ACK Pro集群/GPU资源调度/GPU拓扑感知调度/Pytorch分布式训练使用GPU拓扑感知调度.md)

