---
keyword: [resource-controller, topology-aware CPU scheduling]
---

# resource-controller

This topic lists the latest changes to resource-controller.

## Introduction

resource-controller is the core component that is used to dynamically schedule pods. If you want to enable topology-aware CPU scheduling for a professional Kubernetes cluster, you must install resource-controller.

## Usage notes

For more information about how to use resource-controller, see the following documentation:

-   [Manage system components](/intl.en-US/User Guide for Kubernetes Clusters/Component/Manage system components.md)
-   [Topology-aware CPU scheduling](/intl.en-US/User Guide for Kubernetes Clusters/Scheduling/Resource scheduling/Topology-aware CPU scheduling.md)

## Release notes

**June 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.2.2-0ac97de0-aliyun|-   registry.cn-hangzhou.aliyuncs.com/acs/node-resource-agent:v21.3.9.0-adecd8a-aliyun
-   registry.cn-hangzhou.aliyuncs.com/acs/resource-controller:v1.2.2-0ac97de0-aliyun

|2021-06-21|Memory Bandwidth Allocation \(MBA\) control is supported. Issues related to the change memory feature and containerd are fixed. client-go is upgraded.|No impact on workloads|

**May 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.2.1-d1e280f-aliyun|-   registry.cn-hangzhou.aliyuncs.com/acs/node-resource-agent:v21.3.9.0-adecd8a-aliyun
-   registry.cn-hangzhou.aliyuncs.com/acs/resource-controller:v1.2.1-d1e280f-aliyun

|2021-05-21|L3 control is supported. Topology-aware scheduling is compatible with AMD products.|No impact on workloads|

**April 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.2.0-ec8a979-aliyun|-   registry.cn-hangzhou.aliyuncs.com/acs/node-resource-agent:v21.3.9.0-adecd8a-aliyun
-   registry.cn-hangzhou.aliyuncs.com/acs/resource-controller:v1.2.0-ec8a979-aliyun

|2021-04-20|resources-controller is compatible with containerd. Access to multiple containers and their metadata is supported. The container log can be collected. The resource water marks of tasks can be adjusted in real time.|No impact on workloads|

**March 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.1.0-e7388cb|-   registry.cn-hangzhou.aliyuncs.com/acs/node-resource-agent:v21.3.9.0-adecd8a-aliyun
-   registry.cn-hangzhou.aliyuncs.com/acs/resource-controller:v1.1.0-e7388cb

|2021-03-09|node-agent is released. node-agent allows you to add the `label` field to the ConfigMaps that store information about non-uniform memory access \(NUMA\) nodes.|No impact on workloads|

**January 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.1.0-e7388cb|-   registry.cn-hangzhou.aliyuncs.com/acs/node-resource-agent:121ffbe
-   registry.cn-hangzhou.aliyuncs.com/acs/resource-controller:v1.1.0-e7388cb

|2021-01-26|Distributed deployment of resource-controller is supported. Custom values of the cpuset parameter are supported.|No impact on workloads|

