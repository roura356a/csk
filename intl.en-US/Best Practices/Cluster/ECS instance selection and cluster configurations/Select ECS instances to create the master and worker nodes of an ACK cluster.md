---
keyword: [ECS, create an ACK cluster]
---

# Select ECS instances to create the master and worker nodes of an ACK cluster

This topic describes how to select Elastic Compute Service \(ECS\) instances when you create the master and worker nodes of a Container Service for Kubernetes \(ACK\) cluster.

## Considerations

When you create an ACK cluster, if you specify multiple small-sized ECS instances as the nodes of the cluster, the following issues may occur:

-   The worker nodes that are created based on the small-sized ECS instances can provide only a small number of network resources.
-   If one container consumes most of the resources that are provided by a small-sized ECS instance, the remaining resources of the instance are insufficient for specific operations and thus become idle. For example, the remaining resources cannot be used to create new containers or to restore failed containers. If multiple small-sized ECS instances are used in an ACK cluster, a large number of resources become idle.

We recommend that you use large-sized ECS instances to create the master and worker nodes. This type of ECS instance has the following benefits:

-   Supports a high-bandwidth throughput. High-bandwidth applications can fully utilize the resources on the ECS instance.
-   Increases the number of containers that can communicate with each other on the same ECS instance. This minimizes network latency.
-   Optimizes the process to pull images. After an image is pulled to a large-sized ECS instance, the image can be used to create multiple containers on the ECS instance. To create these containers on a small-sized instance, more pull requests for the image must be made. If the nodes are scaled out in the ACK cluster to create the containers, it takes more time to create the ACK cluster. This also increases network latency.

## Select ECS instances to create the master nodes

Multiple core components are deployed on a master node, such as etcd, kube-apiserver, and kube-controller-manager. These core components are critical to ensure cluster stability. When you create an ACK cluster, you must select the ECS instances that are suitable to create the master nodes. The specifications of the master nodes depend on the size of the ACK cluster. A larger ACK cluster requires the master nodes of higher specifications.

**Note:** The size of the ACK cluster is defined based on multiple factors, such as the number of nodes, the number of pods, the deployment frequency, and the number of requests that are sent to the cluster. In this topic, only the number of nodes is used to define the size of the ACK cluster.

The following table lists multiple common cluster sizes and the specifications of ECS instances that match the cluster sizes. You can select small-sized ECS instances in test environments. The listed specifications can be used to minimize the load on the master nodes.

|Number of nodes in a cluster|Master node specification|
|----------------------------|-------------------------|
|1 to 5 nodes|4 vCPUs and 8 GiB of memory \(We recommend that you do not use 2 vCPUs and 4 GiB of memory.\)|
|6 to 20 nodes|4 vCPUs and 16 GiB of memory|
|21 to 100 nodes|8 vCPUs and 32 GiB of memory|
|100 to 200 nodes|16 vCPUs and 64 GiB of memory|

## Select ECS instances to create worker nodes

-   Determine the specifications of worker nodes based on the total number of vCPUs in the ACK cluster and the maximum failure rate that is tolerated by the ACK cluster.

    For example, if the ACK cluster has 160 vCPUs and supports a maximum failure rate of 10%, we recommend that you select at least 10 ECS instances with 16 vCPUs for each instance. This ensures that at least 144 vCPUs can be consumed during peak hours. This limit is based on the following calculation: 160 vCPUs × 90% = 144 vCPUs. If the ACK cluster supports a maximum failure rate of 20%, we recommend that you select at least five ECS instances with 32 vCPUs for each instance. This ensures that at least 128 vCPUs can be consumed during peak hours. This limit is based on the following calculation: 160 vCPUs × 80% = 128 vCPUs. This way, if an ECS instance has faults, the remaining ECS instances can continue to serve your workloads and ensure high availability.

-   Determine the vCPU-to-memory ratio. For applications that require high memory usage, such as Java applications, we recommend that you select the vCPU-to-memory ratio of 1:8.

## Select ECS Bare Metal instances

We recommend that you use ECS Bare Metal instances in the following scenarios:

-   The ACK cluster requires up to 1,000 vCPUs to serve your workloads. Each ECS Bare Metal instance has at least 96 vCPUs. You can use 10 or 11 ECS Bare Metal instances to create an ACK cluster.
-   Shorten the time that is consumed to scale out a large number of containers. For example, traffic spikes may occur during popular e-commerce sales promotions. To process the traffic spikes, you can expand an ACK cluster based on ECS Bare Metal instances. You can use each ECS Bare Metal instance to add a large number of containers to the cluster.

ECS Bare Metal instances provide the following benefits for an ACK cluster:

-   Ultra-high network performance. Remote direct memory access \(RDMA\) is supported. The Terway plug-in can be used to maximize hardware performance. This allows containers to reach each other across hosts with more than 9 Gbit/s of bandwidth.
-   Optimal computing performance without jitter: ECS Bare Metal instances use chips that are developed by Alibaba Cloud to replace hypervisors. No resources are consumed to create or run virtual machines. The resource preemption issue is resolved.
-   High security: ECS Bare Metal instances support encryption at the physical layer and encryption based on Intel® Software Guard Extensions \(Intel® SGX\). This type of instance also provides trusted computing environments to support blockchain applications.

