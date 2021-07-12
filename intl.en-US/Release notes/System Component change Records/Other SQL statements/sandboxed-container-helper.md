---
keyword: [sandboxed-container-helper, version upgrade]
---

# sandboxed-container-helper

This topic describes the features of sandboxed-container-helper and lists the latest changes to sandboxed-container-helper.

## Introduction

sandboxed-container-helper performs health checks and O&M operations on sandboxed containers. sandboxed-container-helper provides the following features:

-   Provides the Prometheus exporter to collect information about the disk space allocated by Device Mapper. You can deploy ack-arms-prometheus in Container Service for Kubernetes \(ACK\) clusters to monitor the disk space allocated by Device Mapper and configure alerts. For more information, see [Enable ARMS Prometheus](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/Enable ARMS Prometheus.md).
-   Checks and reports unusual events to kube-apiserver, such as storage leaks, container data leaks, and orphaned pods. You can deploy ack-node-problem-detector in ACK clusters to collect and monitor these events. For more information, see [Event monitoring](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/Event monitoring.md).

## Usage notes

By default, sandboxed-container-helper is installed in ACK clusters. You can use the component without extra configurations.

## Release notes

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.0-7a70086-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/sandboxed-container-helper:v1.0.0-7a70086-aliyun|2020-05-12|New features:-   Unusual events, such as container data leaks and orphaned pods, are reported to kube-apiserver.
-   The monitoring of the disk space allocated by Device Mapper is provided.
-   Scripts are provided to fix system issues.

|No impact on workloads|

