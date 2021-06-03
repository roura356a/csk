---
keyword: [sandboxed-container-helper, release notes]
---

# Introduction and release notes for sandboxed-container-helper

This topic describes sandboxed-container-helper and lists the latest changes to sandboxed-container-helper.

## Overview

sandboxed-container-helper performs health checks and operations and maintenance \(O&M\) on sandboxed containers. sandboxed-container-helper provides the following features:

-   sandboxed-container-helper provides Prometheus exporter to collect information about the disk space allocated by Device Mapper. You can deploy ack-arms-prometheus in clusters of Alibaba Cloud Container Service for Kubernetes \(ACK\) to monitor the disk space allocated by Device Mapper and configure alerts. For more information, see [ARMS Prometheus](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/ARMS Prometheus.md).
-   Checks and reports unusual events to kube-apiserver, such as container data leaks and orphaned pods. You can deploy ack-node-problem-detector in ACK clusters to collect and monitor these events. For more information, see [Event monitoring](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/Event monitoring.md).

## Release notes

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.0-7a70086-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/sandboxed-container-helper:v1.0.0-7a70086-aliyun|May 12, 2020|New features:-   Reports unusual events to kube-apiserver, such as container data leaks and orphaned pods.
-   Monitors the disk space that is allocated by Device Mapper.
-   Provides scripts to fix system issues.

|No impact on workloads.|

