---
keyword: [sandboxed-container-controller, Sandboxed-Container]
---

# Introduction and release notes for sandboxed-container-controller

This topic describes sandboxed-container-controller and lists the latest changes to sandboxed-container-controller.

## Introduction

sandboxed-container-controller is a controller component that is provided by the Sandboxed-Container runtime. The component allows you to improve the basic features of sandboxed containers. The following features are supported:

-   Custom kernel parameter settings for pods that run sandboxed containers.
-   Automatic calculation and configuration for virtual machines \(VMs\) that run sandboxed containers.
-   Direct mounting of cloud disks and Network Attached Storage \(NAS\) file systems to sandboxed containers.

## Release notes

**December 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.1.1-55d545f-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/sandboxed-container-controller:v1.1.1-55d545f-aliyun|December 22, 2020|Forcibly overwrites the following annotations that are manually added to a pod configuration: securecontainer.alibabacloud.com/cpus and securecontainer.alibabacloud.com/memory.|This upgrade does not affect your workloads.|

**November 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.1.0-3b3d499-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/sandboxed-container-controller:v1.1.0-3b3d499-aliyun|November 26, 2020|Supports custom kernel parameter settings for pods that run sandboxed containers.|This upgrade does not affect your workloads.|
|v1.0.3-e993d8f-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/sandboxed-container-controller:v1.0.2-8ac82bf-aliyun|November 12, 2020|Supports the PodEraseRuntimeclassRunc admission controller. Docker does not support the RuntimeClass feature. Therefore, when `pod.spec.runtimeClassName` is set to `runc`, this parameter is reset to an empty string.|This upgrade does not affect your workloads.|

**August 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.1-8484958-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/sandboxed-container-controller:v1.0.1-8484958-aliyun|August 26, 2020|Supports Sandboxed-Container V2.0. Supports the PodQuota admission controller. This allows you to set the maximum number of pods that can run sandboxed containers in a specified namespace based on the total CPU and memory resources in pods.|This upgrade does not affect your workloads.|

**June 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.0-e408663-aliyun|registry.cn-beijing.aliyuncs.com/acs/sandboxed-container-controller:v1.0.0-e408663-aliyun|June 10, 2020|Changes the image address of the init container from a public image address to a private image address.|This upgrade does not affect your workloads.|

**March 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.0-a8b276f-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/sandboxed-container-controller:v1.0.0-a8b276f-aliyun|March 26, 2020|Supports the feature of directly mounting cloud disks and Network Attached Storage \(NAS\) file systems to sandboxed containers. This provides the same performance as when these volumes are mounted through the host. This feature allows you to avoid performance loss when volumes are mounted over 9pfs.|This upgrade does not affect your workloads.|

