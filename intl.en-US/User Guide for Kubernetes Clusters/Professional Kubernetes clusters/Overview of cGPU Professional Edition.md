---
keyword: [cGPU Professional Edition, cGPU Basic Edition]
---

# Overview of cGPU Professional Edition

This topic introduces cGPU and describes the benefits of cGPU Professional Edition by comparing it with cGPU Basic Edition.

## Benefits of cGPU Professional Edition

|Benefit|Description|
|-------|-----------|
|Supports graphics processing unit \(GPU\) sharing, scheduling, and memory isolation.|-   Supports GPU sharing, scheduling, and memory isolation on a one-pod-one-GPU basis. This is commonly used in model inference scenarios.
-   Supports GPU sharing, scheduling, and memory isolation on a one-pod-multi-GPU basis. This is commonly used to build the code to train distributed models. |
|Supports flexible GPU sharing and memory isolation policies.|-   Supports GPU allocation by using the binpack and spread algorithms.
    -   Binpack: The system preferably shares one GPU with multiple pods. This applies to scenarios where high GPU utilization is required.
    -   Spread: The system attempts to allocate one GPU to each pod. This applies to scenarios where the high availability of GPUs is required. The system attempts to avoid allocating the same GPU to different pod replicas of an application.
-   Supports GPU sharing without memory isolation. This applies to deep learning scenarios where applications are configured with user-defined isolation systems at the application layer.
-   Supports GPU sharing on multiple GPUs and memory isolation. |
|Supports comprehensive monitoring of GPU resources.|Supports monitoring of both exclusive GPUs and shared GPUs.|

## Comparison between cGPU Basic Edition and cGPU Professional Edition

|Feature|cGPU Professional Edition|cGPU Basic Edition|
|-------|-------------------------|------------------|
|GPU sharing and scheduling on one GPU|Supported|Supported|
|GPU sharing and scheduling on multiple GPUs|Supported|Not supported|
|Memory isolation on one GPU|Supported|Supported|
|Memory isolation on multiple GPUs|Supported|Not supported|
|Monitoring and auto scaling of exclusive GPUs and shared GPUs|Supported|Supported|
|Node pools that support flexible policy configurations|Supported. Allows you to create different GPU policies for a node pool. You can enable GPU sharing with or without memory isolation for a node pool.|Supported. You can configure different GPU policies for a node pool. You can enable GPU sharing with or without memory isolation for a node pool. In addition, you can use the binpack or spread algorithm to allocate GPUs.|
|Allocate GPU memory to pods by using algorithms|Supported. GPUs can be allocated by using the binpack and spread algorithms. You can choose binpack or spread to meet your business requirements.|Supported. By default, GPUs are allocated by using the binpack algorithm.|

**Note:** Different cGPU editions are intended for different types of Kubernetes clusters:

-   cGPU Basic Edition is used after you install ack-ai-installer in a dedicated Kubernetes cluster with GPU-accelerated nodes. For more information, see [Install ack-cgpu](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/GPU scheduling/Shared GPU scheduling/Install ack-cgpu.md).
-   cGPU Professional Edition is used after you install ack-ai-installer in a professional Kubernetes cluster with GPU-accelerated nodes. For more information, see [Install and use ack-ai-installer and the GPU inspection tool](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/GPU scheduling/cGPU Professional Edition/Install and use ack-ai-installer and the GPU inspection tool.md).

## GPU sharing solution by Alibaba Cloud

A key requirement of GPU sharing among multiple pods is to isolate the GPU memory and computing power that are allocated to each pod. When you run multiple containers on one GPU, the GPU resources are allocated to each container as required. However, if one container occupies excessive GPU resources, the performance of the other containers may be affected. To address this issue, many solutions have been developed in the computing industry. Technologies, such as NVIDIA virtual GPU \(vGPU\), NVIDIA Multi-Process Service \(MPS\), rCUDA, and vCUDA, all contribute to fine-grained GPU resource allocation.

The cGPU solution uses the server kernel driver that is developed by Alibaba Cloud to provide more efficient use of the underlying drivers of NVIDIA GPUs. cGPU provides the following features:

-   High compatibility: cGPU is compatible with standard open source solutions, such as Kubernetes and NVIDIA Docker.
-   Ease of use: cGPU provides excellent user experience. To replace a Compute Unified Device Architecture \(CUDA\) library of an AI application, you do not need to recompile the application or create a new container image.
-   Stability: cGPU provides stable underlying operations on NVIDIA GPUs. API operations on CUDA libraries and some private API operations on CUDA Deep Neural Network \(cuDNN\) are difficult to call.
-   Resource isolation: cGPU ensures that the allocated GPU memory and computing capacity do not affect each other.

cGPU provides a cost-effective, reliable, and user-friendly solution that allows you to enable GPU scheduling and memory isolation.

