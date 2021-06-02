---
keyword: [cGPU, overview of cGPU, GPU sharing, memory isolation]
---

# Overview of cGPU

This topic describes the cGPU solution provided by Alibaba Cloud. The cGPU solution helps you better understand and use cGPU.

## Background

ACK provides the open source cGPU solution that allows you to run multiple containers on one GPU in an ACK cluster. You can enable cGPU for Kubernetes clusters that are deployed in Alibaba Cloud, Amazon Web Services \(AWS\), Google Compute Engine \(GCE\), or data centers. cGPU reduces the expenses on GPUs. However, when you run multiple containers on one GPU, the stability of the containers may not be ensured.

To ensure the container stability, you must isolate the GPU resources that are assigned to each container. When you run multiple containers on one GPU, GPU resources are assigned to each container as required. However, if one container occupies excessive GPU resources, the performance of other containers may be affected. To solve this issue, many solutions are provided in the computing industry. For example, NVIDIA virtual GPU \(vGPU\), Multi-Process Service \(MPS\), and vCUDA can enable fine-grained GPU resource allocation.

Alibaba Cloud provides the cGPU solution, which allows you to run multiple containers on one GPU. The cGPU solution also allows you to isolate memory that is requested by each container and partition the computing resources of the GPU.

## Features

The cGPU solution uses the server kernel driver that is developed by Alibaba Cloud to provide more efficient use of the underlying drivers of NVIDIA GPUs. cGPU provides the following features:

-   High compatibility: cGPU is compatible with standard open source solutions, such as Kubernetes and NVIDIA Docker.
-   Ease of use: cGPU adopts a user-friendly design. To replace a Compute Unified Device Architecture \(CUDA\) library of an artificial intelligence \(AI\) application, you do not need to recompile the application or create a new container image.
-   Stability: cGPU provides stable underlying operations on NVIDIA GPUs. API operations on CUDA libraries and some private API operations on cuDNN are difficult to call.
-   Resource isolation: cGPU ensures that the allocated GPU memory and computing capacity do not affect each other.

cGPU provides a cost-effective, reliable, and user-friendly solution that allows you to enable GPU scheduling and memory isolation.

[Install the cGPU component](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Shared GPU scheduling/Install the cGPU component.md)

[Enable GPU sharing](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Shared GPU scheduling/Enable GPU sharing.md)

[Monitor and isolate GPU resources](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Shared GPU scheduling/Monitor and isolate GPU resources.md)

