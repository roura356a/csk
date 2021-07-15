---
keyword: [sandboxed-container-controller, Sandboxed-Container]
---

# sandboxed-container-controller

This topic describes sandboxed-container-controller and lists the latest changes to sandboxed-container-controller.

## Introduction

sandboxed-container-controller is a controller component that is provided by the Sandboxed-Container runtime. The component is used to enhance and extend the basic features of sandboxed containers. The following features are supported:

-   Custom kernel parameter settings for pods that run sandboxed containers.
-   Automatic calculation and configuration for VMs that run sandboxed containers.
-   Direct mounting of disks and Apsara File Storage NAS \(NAS\) file systems to sandboxed containers.

## Usage notes

By default, sandboxed-container-controller is installed in ACK cluster. You can use this component without extra configurations.

## Release notes

**December 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.1.1-55d545f-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/sandboxed-container-controller:v1.1.1-55d545f-aliyun|2020-12-22|The following annotations are forcibly overwritten if they are manually added to pod configurations: securecontainer.alibabacloud.com/cpus and securecontainer.alibabacloud.com/memory.|No impact on workloads|

**November 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.1.0-3b3d499-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/sandboxed-container-controller:v1.1.0-3b3d499-aliyun|2020-11-26|Custom kernel parameter settings are supported for pods that run sandboxed containers.|No impact on workloads|
|v1.0.3-e993d8f-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/sandboxed-container-controller:v1.0.2-8ac82bf-aliyun|2020-11-12|The PodEraseRuntimeclassRunc admission controller is supported. Docker does not support the RuntimeClass feature. Therefore, when `pod.spec.runtimeClassName` is set to `runc`, this parameter is reset to an empty string.|No impact on workloads|

**August 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.1-8484958-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/sandboxed-container-controller:v1.0.1-8484958-aliyun|2020-08-26|Sandboxed-Container 2.0 is supported. The PodQuota admission controller is supported for sandboxed containers. This admission controller can set pod specifications based on the total CPU and memory resources used by sandboxed containers.|No impact on workloads|

**June 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.0-e408663-aliyun|registry.cn-beijing.aliyuncs.com/acs/sandboxed-container-controller:v1.0.0-e408663-aliyun|2020-06-10|The NAS image address of the init container is changed from a public image address to a private image address.|No impact on workloads|

**March 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.0-a8b276f-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/sandboxed-container-controller:v1.0.0-a8b276f-aliyun|2020-03-26|The feature of directly mounting disks and NAS file systems to sandboxed containers is supported. This provides the same performance as when these volumes are mounted through the host. This feature allows you to prevent performance loss when volumes are mounted over 9pfs.|No impact on workloads|

