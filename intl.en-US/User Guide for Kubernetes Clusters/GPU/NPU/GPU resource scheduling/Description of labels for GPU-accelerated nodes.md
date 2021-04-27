---
keyword: [cGPU, topology-aware GPU scheduling, label for GPU-accelerated node]
---

# Description of labels for GPU-accelerated nodes

After ack-ai-installer is installed on a graphics processing unit \(GPU\)-accelerated node, you can add specific labels to the node. This allows you to enable various features, such as GPU sharing, memory isolation, and topology-aware GPU scheduling. This topic describes the labels for GPU-accelerated nodes and the GPU-related features.

## Labels for GPU-accelerated nodes

If you want to use cGPU or topology-aware GPU scheduling on a GPU-accelerated node, you must add specific labels to the node.

**Note:** If a label of a GPU-accelerated node is replaced by a mutually exclusive label, make sure that pods that request extended resources on the node have completed all operations. For example, before label ack.node.gpu.schedule=share is changed to ack.node.gpu.schedule=topology on a GPU-accelerated node, you must make sure that all pods that request aliyun.com/gpu-mem resources on the node have completed all operations.

|Label|Extended resource name|Description|
|-----|----------------------|-----------|
|ack.node.gpu.schedule=share|aliyun.com/gpu-mem|Enables only GPU sharing on a GPU-accelerated node. Memory isolation is not enabled.|
|ack.node.gpu.schedule=cgpu|aliyun.com/gpu-mem|Enables both GPU sharing and memory isolation on a GPU-accelerated node.|
|ack.node.gpu.schedule=topology|aliyun.com/gpu|Enables topology-aware GPU scheduling on a GPU-accelerated node.|
|ck.node.gpu.schedule=default|nvidia.com/gpu|Enables the default GPU scheduling policy on a GPU-accelerated.|
|ack.node.gpu.placement=binpack|\\|Uses the binpack algorithm to schedule GPUs to pods. This applies only when cGPU is used.|
|ack.node.gpu.placement=spread|\\|Uses the spread algorithm to schedule GPUs to pods. This applies only when cGPU is used.|

## GPU sharing and memory isolation

-   The GPU sharing feature allows you to share one GPU of a node among multiple pods. Assume that a node is installed with 2 GPUs \(GPU 1 and GPU 2\) and the total memory of each GPU is 15 GiB. When 2 pods \(Pod 1 and Pod 2\) use GPU 1 at the same time, GPU 1 is shared by Pod 1 and Pod 2.
-   The memory isolation feature allows you to isolate the memory that is allocated from a GPU to each pod that shares the GPU.

    -   Assume that Pod 1 request 2 GiB of memory and Pod 2 requests 3 GiB of memory. When memory isolation is disabled, the amount of memory that can be used by Pod 1 or Pod 2 equals the total amount \(15 GiB\), as shown in the left part of the following figure. In this case, pods may fail to run. For example, if a pod that requests 2 GiB of memory from GPU 1 uses 15 GiB of memory when the pod is running, other pods that share GPU 1 fail to run.
    -   When memory isolation is enabled, each pod uses an exclusive and limited amount of GPU memory. For example, if a pod requests 2 GiB of GPU memory, the amount of memory that can be used by the pod cannot exceed 2 GiB when the pod is running. If the pod attempts to use 3 GiB of GPU memory, the application stops running.
    ![Memory isolation](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0815540161/p185643.png)


## Topology-aware GPU scheduling

Assume that a node is installed with 4 GPUs \(GPU 1, GPU2, GPU 3, and GPU 4\), as shown in the following figure. The bandwidth reaches the highest amount when GPU 1 communicates with GPU 2. The bandwidth reaches the lowest amount when GPU 1 communicates with GPU 4. If a pod requests 2 GPUs, you can use topology-aware GPU scheduling to assign an optimal GPU set to the pod. In this case, the bandwidth reaches the highest amount when GPU 1 communicates with GPU 2. This indicates that the latency is minimized when data is transmitted between GPU 1 and GPU 2. Therefore, GPU 1 and GPU 2 are allocated to the pod.

![Topology-aware GPU scheduling](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0815540161/p185646.png)

## The binpack and spread algorithms

cGPU allows you to use the binpack and spread algorithms to allocate GPUs to pods.

Assume that a node is installed with 2 GPUs and the total memory of each GPU is 15 GiB, Pod 1 requests 2 GiB of GPU memory, and Pod 2 requests 3 GiB of GPU memory, as shown in the following figure.

-   If you use the binpack algorithm, the system preferably allocates memory from one GPU. Another GPU is used only after the memory of first GPU is exhausted. In this case, Pod 1 and Pod 2 are scheduled to GPU 1 in priority, as shown in the left part of the following figure.
-   If you use the spread algorithm, the system attempts to schedule the pods to separate GPUs with best efforts. In this case, Pod 1 is scheduled to GPU 1 and Pod 2 is scheduled to GPU 2, as shown in the right part of the following figure.

![Algorithms](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0815540161/p185649.png)

