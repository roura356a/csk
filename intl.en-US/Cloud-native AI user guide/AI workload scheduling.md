---
keyword: [task scheduling, GPU scheduling, resource utilization]
---

# AI workload scheduling

This topic describes AI workload scheduling and heterogeneous resource scheduling. These features help maximize resource utilization and improve the efficiency of jobs.

## Task scheduling

**Gang Scheduling**

If a job requires all-or-nothing scheduling, all tasks of the job must be scheduled at the same point in time. If only some of the tasks are started, the started tasks must wait until all the remaining tasks are scheduled. In the worst case, all jobs are in the Pending state and the cluster is deadlocked. Alibaba Cloud provides the gang scheduling feature to resolve this issue. For more information, see [Gang scheduling](/intl.en-US/User Guide for Kubernetes Clusters/Scheduling/Workload scheduling/Gang scheduling.md).

**Capacity Scheduling**

A Kubernetes cluster may be managed by multiple users who use cluster resources in different cycles and ways. If the Kubernetes-native object ResourceQuota is used to allocate resources statically, the resource utilization of the cluster is not ideal. To improve the resource utilization of a Kubernetes cluster, Alibaba Cloud provides the capacity scheduling feature to optimize resource allocation. This feature is designed on top of the Yarn capacity scheduler and the Kubernetes scheduling framework. This feature allows you to meet the resource requests in a Kubernetes cluster and improve resource utilization by sharing idle resources. For more information, see [Capacity Scheduling](/intl.en-US/User Guide for Kubernetes Clusters/Scheduling/Workload scheduling/Capacity Scheduling.md).

## Heterogeneous resource scheduling

**GPU sharing and scheduling**

GPU sharing reduces the expenses on GPUs. This feature isolates GPU memory from computing to ensure the stability of applications. For more information, see [Overview](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Shared GPU scheduling/Overview.md). Professional Kubernetes clusters provide rich GPU scheduling policies. GPU sharing and memory isolation on a one-pod-one-GPU basis is commonly used in model inference scenarios. GPU sharing and memory isolation on a one-pod-multi-GPU basis is commonly used to create code builds for distributed model training. GPU scheduling by using the binpack and spread algorithms ensures utilization efficiency and high availability of GPU resources. For more information, see [Overview](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/GPU Resource scheduling/cGPU Professional Edition/Overview.md).

**Topology-aware GPU scheduling**

To ensure the high performance of AI workloads, schedulers choose the optimal scheduling solution based on the topological information of heterogeneous resources on nodes. The information includes how GPUs communicate with each other by using NVLink and PCIe Switches, and the NUMA topology of CPUs. For more information about topology-aware GPU scheduling, see [Overview](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/GPU Resource scheduling/GPU topology-aware scheduling/Overview.md). For more information about topology-aware CPU scheduling, see [Topology-aware CPU scheduling](/intl.en-US/User Guide for Kubernetes Clusters/Scheduling/Resource scheduling/Topology-aware CPU scheduling.md).

