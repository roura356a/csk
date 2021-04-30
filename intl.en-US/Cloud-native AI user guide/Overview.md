---
keyword: [cloud-native AI, heterogeneous resource virtualization, task orchestration]
---

# Overview

Cloud-native AI significantly improves the resource utilization of heterogeneous computing clusters such as GPU and NPU clusters and accelerates the delivery of AI projects. To achieve these goals, cloud-native AI orchestrates and manages data computing tasks, and centrally schedules and maintains various heterogeneous compute resources in containerized environments. Container Service for Kubernetes \(ACK\) provides multiple components, add-ons, extensions, and customizable configurations to support cloud-native AI capabilities. This topic describes the features of the cloud-native AI component set provided by ACK. The following figure shows the system architecture of the cloud-native AI component set.

![K-AI-1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6205579161/p236339.png)

**Note:** Cloud-native AI supports two types of user role:

-   Administrators: responsible for building AI infrastructure and daily administration. For more information, see [Deploy Cloud-native AI Component Set](), [Manage users](), [Manage elastic quota groups](), and [Manage datasets]().
-   Data scientists: use cloud-native AI to submit tasks. For more information, see [Use Arena to submit standalone TensorFlow training jobs](/intl.en-US/Solutions/Deep learning solution/Training/Use Arena to submit standalone TensorFlow training jobs.md), [Use Arena to submit distributed TensorFlow training jobs](/intl.en-US/Solutions/Deep learning solution/Training/Use Arena to submit distributed TensorFlow training jobs.md), [Use Arena to submit standalone PyTorch training jobs](/intl.en-US/Solutions/Deep learning solution/Training/Use Arena to submit standalone PyTorch training jobs.md), and [Use Arena to submit distributed PyTorch training jobs](/intl.en-US/Solutions/Deep learning solution/Training/Use Arena to submit distributed PyTorch training jobs.md).

## Heterogeneous resource efficiency optimization

-   Scheduling of shared GPUs and computing power isolation: Supports various GPU device allocation policies. GPU sharing and memory isolation on a one-pod-one-GPU basis is commonly used in model inference scenarios. GPU sharing and memory isolation on a one-pod-multi-GPU basis is commonly used to build code for distributed model training. GPU scheduling by using the binpack and spread algorithms ensures utilization efficiency and high availability of GPU resources.
    -   Extensive sharing policies: The scheduling capability for shared GPUs provided by Alibaba Cloud allows you to deploy multiple model inference applications on the same GPU. This significantly reduces the costs.
    -   Isolation and stability: The cGPU solution developed by Alibaba Cloud enables isolation between GPU memory and computing power without container modifications.
-   Topology-aware GPU scheduling: Retrieves the topology of heterogeneous compute resources from nodes. The scheduler makes scheduling decisions based on node topology information, NVLINK, PcleSwitch, QPI and RDMA NIC to optimize scheduling options and achieve optimal performance.
-   GPU resource monitoring: Provides monitoring metrics of nodes and applications, detects and sends alerts on device \(software and hardware\) exceptions, and supports dedicated GPUs and shared GPUs.

## Data access performance improvement

Driven by the separation of compute and storage, the open source software Fluid aims to provide a layer of efficient and convenient data abstraction for AI and big data cloud-native applications. The service abstracts data from storage, and uses Alluxio and JIndoFS as cache engines to achieve the following goals:

-   Accelerate access to data by using data affinity scheduling and distributed cache engine acceleration to integrate data with computing.
-   Achieve secure data isolation by data management that is independent of storage and resource isolation by using Kubernetes namespaces.
-   Break down data silos caused by different stores by combining data from different stores in computing.

## AI task scheduling

Supports scheduling policies such as gang scheduling, capacity scheduling, and binpack scheduling to meet the requirements of AI tasks and enhance cluster resource utilization.

-   Gang scheduling: All subtasks of a job must meet the resource requirements before resources are allocated to the subtasks. Otherwise, no resources are allocated to the tasks. This prevents resource deadlocks, in which large jobs use the resources of small jobs.
-   Capacity scheduling: Supports flexible quota settings to improve the overall resource utilization based on resource allocation and sharing.
-   Binpack scheduling: Jobs are first scheduled to one node. If the node has insufficient resources, the remaining jobs are scheduled to another node. This is suitable for training tasks on single machines that have multiple GPU cards. This avoids data transfer across machines. This also prevents resource fragmentation.

## Heterogeneous resources auto scaling

Intelligent load shifting helps avoid cloud resource waste. Elastic model training and model inference are supported.

-   Elastic training: Supports distributed deep learning that provides fault tolerance and flexible scheduling, improves the overall cluster utilization, and reduces the wait time between when a job is submitted and when the job starts to run.
-   Elastic inference: Supports integration with the elasticity features provided by Alibaba Cloud, such as retention of stopped instances, elastic resource pools, timed elasticity, preemptible instances, spot fleets, and automatic scaling of AI inference tasks.

## Observability of cluster tasks, users, and resources

Provides monitoring dashboards of tasks, user quotas, and resources to help you evaluate the input and output.

