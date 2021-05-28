---
keyword: [task scheduling, gang scheduling, capacity scheduling]
---

# Task scheduling

This topic describes the gang scheduling and capacity scheduling features. Gang scheduling is suitable for tasks that require all-or-nothing scheduling. Capacity scheduling improves resource utilization based on elastic quotas.

## Gang Scheduling

Gang scheduling is a scheduling algorithm that schedules all correlated processes to different processors in a parallel system and starts these processes at the same time. This prevents the process group from being blocked when the system fails to start some processes. For example, if you submit a batch job that contains multiple tasks, either all of the tasks are scheduled or none of them is scheduled. Task scheduling in the all-or-nothing scenario is known as gang scheduling. For more information about how to use gang scheduling, see [Gang scheduling](/intl.en-US/User Guide for Kubernetes Clusters/Scheduling/Workload scheduling/Gang scheduling.md).

## Capacity Scheduling

Kubernetes uses the ResourceQuota object to allocate resources statically. As a result, the resource utilization is not ideal. To improve the resource utilization of a Kubernetes cluster, Alibaba Cloud provides the capacity scheduling feature to optimize resource allocation. This feature is developed based on the Yarn capacity scheduler and the Kubernetes scheduling framework. This feature allows you to meet the resource requests in a Kubernetes cluster and improve resource utilization by sharing idle resources. You can use the capacity scheduling feature based on elastic quotas. For more information about how to use capacity scheduling, see [Capacity Scheduling](/intl.en-US/User Guide for Kubernetes Clusters/Scheduling/Workload scheduling/Capacity Scheduling.md).

