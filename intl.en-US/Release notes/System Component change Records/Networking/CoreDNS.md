---
keyword: [CoreDNS, DNS plug-in]
---

# CoreDNS

This topic lists the latest changes to CoreDNS.

## Introduction

CoreDNS is a plug-in used to implement DNS-based service discovery in Container Service for Kubernetes \(ACK\) clusters and edge Kubernetes clusters. CoreDNS follows the specifications of DNS-based service discovery in Kubernetes. For more information, see [Kubernetes DNS-Based Service Discovery](https://github.com/kubernetes/dns/blob/master/docs/specification.md). CoreDNS provides DNS resolution capabilities within Kubernetes clusters. ACK maintains a mapping between CoreDNS versions and Kubernetes versions. When you create or upgrade a Kubernetes cluster, the CoreDNS version that is installed or upgraded depends on the Kubernetes version of the cluster. For more information about the mappings, see [CoreDNS version in Kubernetes](https://github.com/coredns/deployment/blob/master/kubernetes/CoreDNS-k8s_version.md).

## Usage notes

For more information about the features and usage notes of CoreDNS, see [Introduction and configuration of the DNS service in ACK clusters](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Introduction and configuration of the DNS service in ACK clusters.md).

## Precautions

Before you upgrade CoreDNS, you must read the precautions. For more information, see [Precautions for upgrading CoreDNS](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Precautions for upgrading CoreDNS.md).

## Release notes

**July 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|V1.7.0.0-f59c03d-aliyun \(for non-edge Kubernetes clusters.\)|registry.\{\{.Region\}\}.aliyuncs.com/acs/coredns:v1.7.0.0-f59c03d-aliyun|2021-07-08|The default duration of graceful shutdown for CoreDNS and the memory limit of containers in the CoreDNS Deployment are modified.|[Precautions](#section_pmu_oyp_5v8)|

**April 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|V1.6.7.edge \(for edge Kubernetes clusters\)|registry.\{\{.Region\}\}.aliyuncs.com/acs/coredns:1.6.7.edge|2021-04-23|This version is optimized based on open source CoreDNS 1.6.7. For more information, see [CoreDNS-1.6.7 Release](https://coredns.io/2020/01/28/coredns-1.6.7-release/).|No impact on workloads|

**March 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|V1.7.0 \(for non-edge Kubernetes clusters\)|registry.\{\{.Region\}\}.aliyuncs.com/acs/coredns:1.7.0|2021-03-18|-   The deprecated upstream plug-in is no longer compatible. If the upstream plug-in is specified in the Corefile configurations, the upstream plug-in will be automatically deleted in a secure way when CoreDNS is upgraded.
-   The names of metrics are updated. If your monitoring system is reliant on CoreDNS metrics, you must update the metric names. For more information, see [Metric changes](https://coredns.io/2020/06/15/coredns-1.7.0-release/#metric-changes).

|No impact on workloads|

**November 2018**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|V1.6.7 \(for non-edge Kubernetes clusters\)|registry.\{\{.Region\}\}.aliyuncs.com/acs/coredns:1.6.7|2018-11-28|This version is optimized based on open source CoreDNS 1.6.7. For more information, see [CoreDNS-1.6.7 Release](https://coredns.io/2020/01/28/coredns-1.6.7-release/).|No impact on workloads|

