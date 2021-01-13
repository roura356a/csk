---
keyword: [sandboxed-container-controller, Sandboxed-Container]
---

# Introduction and release notes for sandboxed-container-controller

This topic describes sandboxed-container-controller and lists the latest changes to sandboxed-container-controller.

## Introduction

sandboxed-container-controller is a controller component provided by the Sandboxed-Container runtime. You can use this component to directly mount NAS file systems and Alibaba Cloud disks to sandboxed containers. This offers the same storage I/O performance as when volumes are mounted through the host.

## Release notes

**August 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.1-8484958-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/sandboxed-container-controller:v1.0.1-8484958-aliyun|August 26, 2020|Supports Sandboxed-Container V2.0. Supports the PodQuota feature. This allows you to set the maximum number of pods that can run sandboxed containers in a specified namespace based on the total CPU and memory resources in pods.|No impact on workloads.|

**June 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.0-e408663-aliyun|registry.cn-beijing.aliyuncs.com/acs/sandboxed-container-controller:v1.0.0-e408663-aliyun|June 10, 2020|Changes the image address of the init container from a public image address to a private image address.|No impact on workloads.|

**March 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.0-a8b276f-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/sandboxed-container-controller:v1.0.0-a8b276f-aliyun|March 26, 2020|Supports the feature of mounting Alibaba Cloud disks and NAS file systems directly to sandboxed containers. This provides the same performance as when these volumes are mounted through the host. This feature allows you to avoid performance loss when volumes are mounted over 9pfs.|No impact on workloads.|

