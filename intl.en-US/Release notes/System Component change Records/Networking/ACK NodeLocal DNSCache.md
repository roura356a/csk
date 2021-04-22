---
keyword: [ACK NodeLocal DNSCache, local DNS caching solution]
---

# ACK NodeLocal DNSCache

This topic lists the latest changes to the NodeLocal DNSCache component provided by Container Service for Kubernetes \(ACK\).

## Introduction

ACK NodeLocal DNSCache is a local DNS caching solution developed based on the open source NodeLocal DNSCache project. To use ACK NodeLocal DNSCache, refer to the ack-node-local-dns Helm chart. This solution consists of a DNS cache that runs as a DaemonSet and an admission controller that runs as a Deployment to dynamically inject data to DNSConfig. The admission controller listens on requests from pods and dynamically modifies DNSConfig. This enables pods to use local cache to accelerate DNS lookups.

## Usage notes

You can use the component to enable pods to access the local DNS cache on cluster nodes. For more information, see [Deploy Node Local DNS in an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Deploy Node Local DNS in an ACK cluster.md).

## Release notes

**April 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|1.3.3|-   DNS cache: registry.cn-hangzhou.aliyuncs.com/acs/k8s-dns-node-cache:v1.15.13-6-7e6778ac
-   Admission controller: registry.cn-hangzhou.aliyuncs.com/acs/node-local-dns-admission-controller:v1.0.2-8b46b2f-aliyun

|2021-04-21|First version.|No impact on workloads.|

