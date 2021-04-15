---
keyword: [resource-controller, topology-aware CPU scheduling]
---

# resource-controller

This topic lists the latest changes to resource-controller.

## Introduction

resource-controller is the core component that is used to dynamically schedule pods. If you want to enable topology-aware CPU scheduling for a professional Kubernetes cluster, you must install resource-controller.

## Instruction

For more information about how to use resource-controller, see [Topology-aware CPU scheduling](/intl.en-US/User Guide for Kubernetes Clusters/Scheduling/Resource scheduling/Topology-aware CPU scheduling.md).

## Release notes

**March 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.1.0-e7388cb|-   registry.cn-hangzhou.aliyuncs.com/acs/node-resource-agent:v21.3.9.0-adecd8a-aliyun
-   registry.cn-hangzhou.aliyuncs.com/acs/resource-controller:v1.1.0-e7388cb

|2021-03-09|node-agent is released. It allows you to add `labels` to ConfigMaps that store information about non-uniform memory access \(NUMA\) nodes.|N/A|

**January 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.1.0-e7388cb|-   registry.cn-hangzhou.aliyuncs.com/acs/node-resource-agent:121ffbe
-   registry.cn-hangzhou.aliyuncs.com/acs/resource-controller:v1.1.0-e7388cb

|2021-01-26|Distributed deployments and custom values of the cpuset parameter are supported by resource-controller.|N/A|

