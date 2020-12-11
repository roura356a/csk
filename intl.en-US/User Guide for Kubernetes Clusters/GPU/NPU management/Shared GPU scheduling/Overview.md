---
keyword: [cGPU, benefits, background, GPU sharing]
---

# Overview

This topic describes Alibaba Cloud cGPU, which allows you to share GPU resources.

## Background

Container Service for Kubernetes \(ACK\) provides the open source cGPU solution that allows you to run multiple containers on one GPU in an ACK cluster. You can enable cGPU for container clusters that are deployed on Alibaba Cloud, Amazon Web Services \(AWS\), Google Compute Engine \(GCE\), or data centers. cGPU reduces the expenses on GPUs. However, when you run multiple containers on one GPU, it is a challenge to ensure container stability.

To ensure the container stability, you must isolate the GPU resources that are assigned to each container. When you run multiple containers on one GPU, GPU resources are assigned to each container as required. However, if one container occupies excessive GPU resources, the performance of other containers may be affected. To solve this issue, many solutions have been provided in the computing industry. For example, NVIDIA virtual GPU \(vGPU\), Multi-Process Service \(MPS\), and vCUDA can enable fine-grained GPU resource allocation.

## Features

The cGPU solution is provided by Alibaba Cloud. The cGPU solution uses the server kernel driver that is developed by Alibaba Cloud to provide more efficient use of the underlying drivers of NVIDIA GPUs. cGPU provides the following features:

-   High compatibility: cGPU is compatible with standard open source solutions, such as Kubernetes and NVIDIA Docker.
-   Ease-of-use: cGPU adopts a user-friendly design. To replace a Compute Unified Device Architecture \(CUDA\) library of an artificial intelligence \(AI\) application, you do not need to re-compile the application or create a new container image.
-   Stability: cGPU provides stable underlying operations on NVIDIA GPUs. API operations on CUDA libraries and some private API operations on cuDNN are difficult to call.
-   Resource isolation: cGPU ensures that the allocated GPU memory and computing capacity do not affect each other.

Based on cGPU, ACK enables GPU sharing and the scheduling of multiple tasks to one GPU. This enables GPU sharing and memory isolation for scheduled Kubernetes resources and the container runtime. This provides low-cost, reliable, and user-friendly GPU sharing and memory isolation for large scale business.

**Related topics**  


[Install a shared GPU](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU management/Shared GPU scheduling/Install a shared GPU.md)

[Enable GPU sharing](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU management/Shared GPU scheduling/Enable GPU sharing.md)

[Monitor and isolate GPU resources](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU management/Shared GPU scheduling/Monitor and isolate GPU resources.md)

