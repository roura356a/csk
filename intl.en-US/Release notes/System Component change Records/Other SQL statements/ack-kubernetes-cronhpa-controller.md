---
keyword: [ack-kubernetes-cronhpa-controller introduction, ack-kubernetes-cronhpa-controller usage notes, ack-kubernetes-cronhpa-controller release notes]
---

# ack-kubernetes-cronhpa-controller

The ack-kubernetes-cronhpa-controller component is used to enable resource scaling by schedule. This topic introduces ack-kubernetes-cronhpa-controller and describes the usage notes and release notes of the component.

## Introduction

The ack-kubernetes-cronhpa-controller component is a Kubernetes Horizontal Pod Autoscaler \(HPA\) controller that you can use to scale the number of pods by schedule. Cron Horizontal Pod Autoscaler \(CronHPA\) automatically scales the number of pods based on a schedule. You can use CronHPA with Kubernetes objects whose subresources can be scaled. These objects include Deployments and StatefulSets. The subresources must be open source projects on GitHub.

## Usage notes

For more information about the usage notes of ack-kubernetes-cronhpa-controller, see [CronHPA](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/CronHPA.md).

## Release notes

**May 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.4.0-a2f4954d-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/kubernetes-cronhpa-controller:v1.4.0-a2f4954d-aliyun|2021-05-27|The authorization issue related to elastic workloads is fixed.|No impact on workloads|

**December 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.4.0-fc4f6060-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/kubernetes-cronhpa-controller:v1.4.0-fc4f6060-aliyun|2020-12-18|-   Basic features of CronHPA are supported.
-   Compatibility with HPA is supported.

|No impact on workloads|

