---
keyword: [metrics-server, usage notes, release notes]
---

# metrics-server

The metrics-server component is used to collect resource usage data. You can use metrics-server to collect and monitor resource metrics of your cluster. This topic introduces metrics-server and describes the usage notes and release notes for metrics-server.

## Introduction

The metrics-server component is a resource monitoring tool built based on the open source Metrics Server by Container Service for Kubernetes \(ACK\). The component collects resource usage metrics for all pods in your cluster and enables Horizontal Pod Autoscaler \(HPA\). You can call the Metrics API to retrieve monitoring metrics.

For more information about the open source Metrics Server, see [Kubernetes Metrics Server](https://github.com/kubernetes-sigs/metrics-server).

## Usage notes

The metrics-server component collects resource usage metrics of all pods in the cluster. For more information, see [Monitor basic resources](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/Monitor basic resources.md).

## Release notes

**May 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.3.8.5-307cf45-aliyun|registry.\{REGION\}.aliyuncs.com/acs/metrics-server:v0.3.8.5-307cf45-aliyun|May 2021|-   Compatibility with Kubernetes earlier than 1.20 is added.
-   Container Service Monitoring is enabled in CloudMonitor.

|No impact on workloads|

**April 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.2.2-b4bf266-aliyun|registry.\{REGION\}.aliyuncs.com/acs/metrics-server:v0.2.2-b4bf266-aliyun|April 2021|-   Collecting metrics from Windows and Linux nodes is supported.
-   The following scaling periods are supported: 15 seconds, 20 seconds, 30 seconds, and 60 seconds.
-   The issue that HPA adds excess pods during an application rolling update is fixed.

|We recommend that you upgrade the component to the latest version.|

**Related topics**  


[FAQ about metrics-server](/intl.en-US/User Guide for Kubernetes Clusters/Component/FAQ about metrics-server.md)

