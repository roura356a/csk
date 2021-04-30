---
keyword: [CoreDNS, DNS plug-in]
---

# CoreDNS

This topic lists the latest changes to CoreDNS.

## Introduction

CoreDNS is the plug-in used to implement DNS-based service discovery in Container Service for Kubernetes \(ACK\) clusters and edge Kubernetes clusters. CoreDNS follows the specifications of DNS-based service discovery in Kubernetes. For more information, see [Kubernetes DNS-Based Service Discovery](https://github.com/kubernetes/dns/blob/master/docs/specification.md). CoreDNS provides DNS resolution capabilities within Kubernetes clusters. ACK maintains a mapping between CoreDNS versions and Kubernetes versions. When you create or upgrade a Kubernetes cluster, the CoreDNS version that is installed or upgraded is dependent on the Kubernetes version of the cluster. For more information about the mappings, see [CoreDNS version in Kubernetes](https://github.com/coredns/deployment/blob/master/kubernetes/CoreDNS-k8s_version.md).

## Instruction

For more information about the features of CoreDNS and how to use them, see [Introduction and configuration of the DNS service in ACK clusters](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Introduction and configuration of the DNS service in ACK clusters.md).

## Release notes

**April 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|V1.7.0 \(for non-edge Kubernetes clusters\)|registry.\{\{.Region\}\}.aliyuncs.com/acs/coredns:1.7.0|April 2021|-   The deprecated upstream plug-in is no longer compatible. If the upstream plug-in is specified in the Corefile configurations, it will be automatically deleted in a secure way during the upgrade of CoreDNS.
-   The names of metrics are updated. If your monitoring system is dependent on CoreDNS metrics, we recommend that you use the updated names. For more information, see [Metric changes](https://coredns.io/2020/06/15/coredns-1.7.0-release/#metric-changes).

|No impact on workloads.|

**January 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|V1.6.7.edge \(for edge Kubernetes clusters\)|registry.\{\{.Region\}\}.aliyuncs.com/acs/coredns:1.6.7.edge|January 2021|This version is optimized on top of open source CoreDNS 1.6.7. For more information, see [CoreDNS-1.6.7 Release](https://coredns.io/2020/01/28/coredns-1.6.7-release/).|No impact on workloads.|
|V1.6.7 \(for non-edge Kubernetes clusters\)|registry.\{\{.Region\}\}.aliyuncs.com/acs/coredns:1.6.7|January 2021|This version is optimized on top of open source CoreDNS 1.6.7. For more information, see [CoreDNS-1.6.7 Release](https://coredns.io/2020/01/28/coredns-1.6.7-release/).|No impact on workloads.|

