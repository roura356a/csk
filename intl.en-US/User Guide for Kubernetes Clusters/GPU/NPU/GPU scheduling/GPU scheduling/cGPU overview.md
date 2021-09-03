---
keyword: [cGPU, cGPU overview, GPU sharing, memory isolation]
---

# cGPU overview

This topic describes the cGPU solution provided by Alibaba Cloud and helps you better understand and use cGPU.

## Background

ACK provides the open source cGPU solution that allows you to run multiple containers on one GPU in an ACK cluster. You can enable cGPU for Kubernetes clusters that are deployed in Alibaba Cloud, Amazon Web Services \(AWS\), Google Compute Engine \(GCE\), or data centers. cGPU reduces the expenses on GPUs. However, when you run multiple containers on one GPU, the stability of the containers may not be ensured.

To ensure the container stability, you must isolate the GPU resources that are assigned to each container. When you run multiple containers on one GPU, GPU resources are assigned to each container as required. However, if one container occupies excessive GPU resources, the performance of other containers may be affected. To solve this issue, many solutions are provided in the computing industry. For example, NVIDIA vGPU, Multi-Process Service \(MPS\), and vCUDA enable fine-grained GPU resource allocation.

Alibaba Cloud provides the cGPU solution, which allows you to run multiple containers on one GPU. The cGPU solution also allows you to isolate the GPU memory that is allocated to each container and partition the computing capacity of the GPU.

## Features

The cGPU solution uses the server kernel driver that is developed by Alibaba Cloud to provide more efficient use of the underlying drivers of NVIDIA GPUs. cGPU provides the following features:

-   High compatibility: cGPU is compatible with standard open source solutions, such as Kubernetes and NVIDIA Docker.
-   Ease of use: cGPU provides excellent user experience. To replace a Compute Unified Device Architecture \(CUDA\) library of an AI application, you do not need to recompile the application or create a new container image.
-   Stability: cGPU provides stable underlying operations on NVIDIA GPUs. API operations on CUDA libraries and some private API operations on CUDA Deep Neural Network \(cuDNN\) are difficult to call.
-   Resource isolation: cGPU ensures that the allocated GPU memory and computing capacity do not affect each other.

cGPU provides a cost-effective, reliable, and user-friendly solution that allows you to enable GPU scheduling and memory isolation.

**Note:** The installation steps and supported editions of cGPU vary based on cluster types.

-   cGPU Basic Edition: For more information about how to install cGPU in dedicated Kubernetes clusters with GPU-accelerated nodes, see the following topics:
    -   [Install ack-cgpu](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/GPU scheduling/Shared GPU scheduling/Install ack-cgpu.md)
    -   [Enable GPU sharing](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/GPU scheduling/Shared GPU scheduling/Enable GPU sharing.md)
    -   [Monitor and isolate GPU resources](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/GPU scheduling/Shared GPU scheduling/Monitor and isolate GPU resources.md)
-   cGPU Professional Edition: For more information about how to install cGPU in professional Kubernetes clusters, see the following topics:
    -   [Install and use ack-ai-installer and the GPU inspection tool](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/GPU scheduling/cGPU Professional Edition/Install and use ack-ai-installer and the GPU inspection tool.md)
    -   [Enable GPU sharing](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/GPU scheduling/cGPU Professional Edition/Enable GPU sharing.md)
    -   [Use cGPU to achieve GPU sharing based on multiple GPUs](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/GPU scheduling/cGPU Professional Edition/Use cGPU to achieve GPU sharing based on multiple GPUs.md)

