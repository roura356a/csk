---
keyword: [资源调度, CPU拓扑感知调度, Kube-scheduler]
---

# CPU拓扑感知调度

本文介绍ACK基于新版的Scheduling framework实现了CPU拓扑感知调度，针对CPU敏感型的工作负载提供更好的性能。

## CPU拓扑感知调度

Kubernetes的Node节点会运行多个Pod，其中会有部分的Pod属于CPU密集型的工作负载。在这种情况下，Pod之间会争抢节点的CPU资源。当争抢剧烈的时候，Pod会在不同的CPU Core之间进行频繁的切换，更糟糕的是在NUMA Node之间的切换。这种大量的上下文切换，会影响程序运行的性能。Kubernetes虽然有CPU Manager解决方案处理以上问题，但是因为CPU Manager特性是节点级别的CPU调度选择，所以无法在集群维度中选择最优的CPU Core组合。同时CPU Manager特性要求Pod是Guaranteed时（Pod中的每个容器必须指定CPU Request和CPU Limit，并且两者要相等）才能生效，且无法适用于所有类型的Pod。

在以下场景中，建议使用CPU拓扑感知调度：工作负载为计算密集型、应用程序对CPU敏感、运行在神龙裸金属等多核机器上。

更多信息，请参见[CPU拓扑感知调度](/cn.zh-CN/Kubernetes集群用户指南/调度/CPU和内存调度/CPU拓扑感知调度.md)。

