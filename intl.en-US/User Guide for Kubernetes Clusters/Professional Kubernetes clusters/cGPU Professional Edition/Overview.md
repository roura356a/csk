---
keyword: [cGPU Professional Edition, cGPU Basic Edition, cGPU]
---

# Overview

This topic describes the benefits of cGPU Professional Edition and compares it with cGPU Basic Edition.

## Benefits of cGPU Professional Edition

|Benefit|Description|
|-------|-----------|
|Supports graphics processing unit \(GPU\) sharing and memory isolation.|-   Supports GPU sharing and memory isolation on a one-pod-one-GPU basis. This is commonly used in model inference scenarios.
-   Supports GPU sharing and memory isolation on a one-pod-multi-GPU basis. This is commonly used to build code of distributed training models. |
|Supports flexible GPU sharing and memory isolation policies.|-   Supports GPU scheduling by using the binpack and spread algorithms.
    -   Binpack: The system preferably allocates one GPU to multiple pods. This applies to scenarios where high GPU utilization is required.
    -   Spread: The system attempts to allocate one GPU to each pod. This applies to scenarios where the high availability of GPUs is required. The system attempts to avoid scheduling the same GPU to different pods of an application.
-   Supports GPU sharing without memory isolation. This applies to deep learning scenarios where applications are configured with user-defined isolation systems at the application layer.
-   Supports GPU sharing on multiple GPUs and memory isolation. |
|Supports comprehensive monitoring on GPU resources.|Supports monitoring on both exclusive GPUs and shared GPUs.|

## Comparison between cGPU Basic Edition and cGPU Professional Edition

|Feature|cGPU Professional Edition|cGPU Basic Edition|
|-------|-------------------------|------------------|
|GPU sharing on one GPU|Supported|Supported|
|GPU sharing on multiple GPUs|Supported|Not supported|
|Memory isolation on one GPU|Supported|Supported|
|Memory isolation on multiple GPUs|Supported|Not supported|
|Monitoring and auto scaling for exclusive GPUs and shared GPUs|Supported|Supported|
|Multiple scheduling policies for one node pool|Supported. You can create different GPU scheduling policies for a node pool. You can enable GPU sharing with or without memory isolation for a node pool.|Supported. You can create different GPU scheduling policies for a node pool. You can enable GPU sharing with or without memory isolation for a node pool. In addition, you can use the binpack or spread algorithm to schedule GPUs.|
|Scheduling GPUs to pods based on algorithms|Supported. GPUs can be scheduled based on the binpack and spread algorithms. You can choose binpack or spread based on your business requirements.|Supported. By default, GPUs are scheduled based on the binpack algorithm.|

## cGPU

A key requirement of GPU sharing among multiple pods is to isolate the GPU memory and computing power that are allocated to each pod. When you run multiple containers on one GPU, GPU resources are allocated to each container as required. However, if one container occupies excessive GPU resources, the performance of other containers may be affected. To address this issue, many solutions have been developed in the computing industry. Technologies such as NVIDIA virtual GPU \(vGPU\), NVIDIA Multi-process Service \(MPS\), rCUDA, and vCUDA all contribute to the use of fine-grained GPU resource allocation.

The cGPU solution is provided by Alibaba Cloud. The cGPU solution uses the server kernel driver that is developed by Alibaba Cloud to provide more efficient use of the underlying drivers of NVIDIA GPUs. cGPU provides the following features:

-   High compatibility: cGPU is compatible with standard open source solutions, such as Kubernetes and NVIDIA Docker.
-   Ease-of-use: cGPU adopts a user-friendly design. To replace a Compute Unified Device Architecture \(CUDA\) library of an artificial intelligence \(AI\) application, you do not need to re-compile the application or create a new container image.
-   Stability: cGPU provides stable underlying operations on NVIDIA GPUs. API operations on CUDA libraries and some private API operations on cuDNN are difficult to call.
-   Resource isolation: cGPU ensures that the allocated GPU memory and computing capacity do not affect each other.

Based on cGPU, ACK enables GPU sharing and the scheduling of multiple tasks to one GPU. This enables GPU sharing and memory isolation for scheduled Kubernetes resources and the container runtime. This provides low-cost, reliable, and user-friendly GPU sharing and memory isolation for large scale business.

