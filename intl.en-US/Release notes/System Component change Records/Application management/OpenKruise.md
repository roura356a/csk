---
keyword: [OpenKruise, release notes]
---

# OpenKruise

OpenKruise is a set of standard extensions for Kubernetes. It can be used with native Kubernetes to efficiently manage application pods, sidecar containers, and image distribution. This topic introduces OpenKruise and describes the usage notes and release notes of OpenKruise.

## Introduction

OpenKruise is an open source automation engine provided by Alibaba Cloud for cloud-native applications. It is used as a deployment base to migrate the business of Alibaba Group to the cloud. OpenKruise has joined the Cloud Native Computing Foundation \(CNCF\) Sandbox project. OpenKruise contains a variety of custom workloads. You can use the workloads to deploy and manage stateless applications, stateful applications, sidecar containers, and daemon applications. OpenKruise also supports advanced strategies such as in-place upgrades, canary releases, stream upgrades, and priority configuration.

## Usage notes

For more information about the usage notes of OpenKruise, see [Use OpenKruise to deploy cloud-native applications](/intl.en-US/User Guide for Kubernetes Clusters/Release management/Use OpenKruise to deploy cloud-native applications.md).

## Release notes

**May 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.9.0|registry.cn-hangzhou.aliyuncs.com/acs/kruise-manager:v0.9.0|2021-05-20|New features:-   Container restart and recreation are supported.
-   Cascading deletion protection is supported.
-   Pod deletion based on priorities, image pre-download, and efficient rollback are supported by CloneSet.
-   Hot upgrades of mesh containers are supported by SidecarSet.

|No impact on workloads|

