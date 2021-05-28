---
keyword: [resource scheduling, topology-aware CPU scheduling, Kube-scheduler]
---

# Topology-aware CPU scheduling

Container Service for Kubernetes \(ACK\) provides the topology-aware CPU scheduling feature based on the new Kubernetes scheduling framework. This feature can improve the performance of CPU-sensitive workloads.

## Topology-aware CPU scheduling

Multiple pods may run on a node in a Kubernetes cluster and some pods may belong to CPU-intensive workloads. In this case, pods compete for CPU resources. When this situation becomes intensive, the CPU cores that are allocated to each pod may be frequently changed. The situation intensifies when the Non-Uniform Memory Access \(NUMA\) nodes are used. These changes degrade the performance of the workloads. The Kubernetes CPU manager provides a CPU scheduling solution to fix this issue within a node. However, the Kubernetes CPU manager cannot find an optimal solution for allocating CPU cores within a cluster. The Kubernetes CPU manager works only on guaranteed pods and does not apply to all types of pods. In a guaranteed pod, each container is configured with requests and limits on CPU resources. In addition, the requests and limits are set to the same value.

We recommend that you use topology-aware CPU scheduling if your workloads are compute-intensive, CPU-sensitive, and run on ECS Bare Metal instances that have multiple CPU cores.

For more information, see [Topology-aware CPU scheduling](/intl.en-US/User Guide for Kubernetes Clusters/Scheduling/Resource scheduling/Topology-aware CPU scheduling.md).

