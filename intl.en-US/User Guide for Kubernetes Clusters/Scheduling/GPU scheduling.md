---
keyword: [GPU scheduling, default GPU scheduling, GPU sharing and scheduling, Topology-aware GPU scheduling]
---

# GPU scheduling

This topic describes the various methods of GPU scheduling, such as the default GPU scheduling, GPU sharing and scheduling, and topology-aware GPU scheduling. GPU sharing and scheduling improve the utilization of GPU resources. Topology-aware GPU scheduling accelerates task processing.

## Default GPU scheduling

After you create a Kubernetes cluster with GPU-accelerated nodes, you can set up a GPU-accelerated environment to run TensorFlow. For more information about how to schedule dedicated GPU resources, see [GPU scheduling for ACK clusters with GPU-accelerated nodes](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/GPU scheduling for ACK clusters with GPU-accelerated nodes.md).

You can also enable custom GPU scheduling based on node labels. For more information, see [Use labels to schedule pods to GPU-accelerated nodes](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Use labels to schedule pods to GPU-accelerated nodes.md).

## GPU sharing and scheduling

Container Service for Kubernetes \(ACK\) provides the open source cGPU solution that allows you to share one GPU among multiple containers in an ACK cluster. You can enable cGPU for container clusters that are deployed in Alibaba Cloud, Amazon Web Services \(AWS\), Google Compute Engine \(GCE\), or data centers. cGPU enables GPU sharing and reduces the cost of GPU resources. cGPU also enables the isolation of GPU resources allocated to multiple containers when one GPU is shared. This prevents the issue in which some containers consume excessive resources and other containers run with insufficient resources. cGPU also provides fine-grained GPU utilization. You can refer to the following topics for further details:

For more information about cGPU, see [Overview of cGPU](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Shared GPU scheduling/Overview of cGPU.md).

For more information about how to enable cGPU, see [Install the cGPU component](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Shared GPU scheduling/Install the cGPU component.md). For more information about how to disable cGPU, see [Disable the memory isolation capability of cGPU](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Shared GPU scheduling/Disable the memory isolation capability of cGPU.md).

For more information about how to use cGPU, see [Enable GPU sharing](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Shared GPU scheduling/Enable GPU sharing.md), [Monitor and isolate GPU resources](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Shared GPU scheduling/Monitor and isolate GPU resources.md), and [Use node pools to control cGPU](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Shared GPU scheduling/Use node pools to control cGPU.md).

## Topology-aware GPU scheduling

Kubernetes is unaware of the topology of GPU resources on nodes. Therefore, Kubernetes schedules GPU resources in a random manner. As a result, the GPU acceleration for training jobs considerably varies based on the scheduling results of GPU resources. To avoid this situation, ACK supports topology-aware GPU scheduling based on the scheduling framework of Kubernetes. You can use this feature to select a combination of GPUs from GPU-accelerated nodes to achieve optimal GPU acceleration for training jobs. For more information about how to use topology-aware GPU scheduling, see the following topics:

-   [Overview of topology-aware GPU scheduling](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/GPU scheduling/GPU topology-aware scheduling/Overview of topology-aware GPU scheduling.md)
-   [Install ack-ai-installer](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/GPU scheduling/GPU topology-aware scheduling/Install ack-ai-installer.md)
-   [Use topology-aware GPU scheduling to achieve optimal GPU acceleration for TensorFlow distributed jobs](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/GPU scheduling/GPU topology-aware scheduling/Use topology-aware GPU scheduling to achieve optimal GPU acceleration for TensorFlow distributed jobs.md)
-   [Use topology-aware GPU scheduling to achieve optimal GPU acceleration for PyTorch distributed jobs](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/GPU scheduling/GPU topology-aware scheduling/Use topology-aware GPU scheduling to achieve optimal GPU acceleration for PyTorch distributed jobs.md)

