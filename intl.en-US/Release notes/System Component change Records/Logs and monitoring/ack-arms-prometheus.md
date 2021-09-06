---
keyword: [ack-arms-prometheus, ARMS Prometheus]
---

# ack-arms-prometheus

The ack-arms-prometheus component is a resource monitoring tool used by Application Real-Time Monitoring Service \(ARMS\) Prometheus to monitor Container Service for Kubernetes \(ACK\) clusters. This topic introduces ack-arms-prometheus and describes the usage notes and release notes for ack-arms-prometheus.

## Introduction

The ack-arms-prometheus component is a resource monitoring tool provided by ACK for integration with ARMS Prometheus. You must install this component if you want to enable Prometheus monitoring for an ACK cluster. The component uploads data collected from your cluster to ARMS Prometheus. ARMS Prometheus then generates data links based on collected data. You can view dashboards and data links in ARMS Prometheus.

## Usage notes

For more information about ack-arms-prometheus, see [Enable ARMS Prometheus](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/Enable ARMS Prometheus.md).

## Release notes

**July 2021**

|Version|Release date|Description|Impact|
|-------|------------|-----------|------|
|0.1.8|July 2021|Compatibility is improved.|No impact on workloads|

**October 2020**

|Version|Release date|Description|Impact|
|-------|------------|-----------|------|
|0.1.5|October 2020|-   Clusters of Kubernetes 1.18 are supported.
-   Image pulling through internal endpoints is supported.

|No impact on workloads

We recommend that you upgrade the component to the latest version. |

**July 2020**

|Version|Release date|Description|Impact|
|-------|------------|-----------|------|
|0.1.4|July 2020|-   Out-of-the-box Kubernetes monitoring, including pod monitoring, node monitoring, and resource monitoring. This feature is used to monitor the runtime of Kubernetes containers where applications are deployed.
-   Web-based component monitoring, including nine common components such as MySQL, Redis, Kafka, ZooKeeper, and NGINX. This feature is suitable for scenarios where applications depend on middleware.
-   Fully-managed Prometheus monitoring system, including the prometheus.yaml collection rule, Grafana dashboard, and alerting system. This feature can meet the needs of migrating data from on-premises Prometheus systems to Alibaba Cloud.

|No impact on workloads

We recommend that you upgrade the component to the latest version. |

**April 2020**

|Version|Release date|Description|Impact|
|-------|------------|-----------|------|
|0.1.3|April 2020|Control of resources used by the agent is supported.|No impact on workloads

We recommend that you upgrade the component to the latest version. |

**August 2019**

|Version|Release date|Description|Impact|
|-------|------------|-----------|------|
|0.1.2|August 2019|First release.|No impact on workloads

We recommend that you upgrade the component to the latest version. |

**Related topics**  


[FAQ about ack-arms-prometheus](/intl.en-US/User Guide for Kubernetes Clusters/Component/FAQ about ack-arms-prometheus.md)

