---
keyword: [ack-kubernetes-webhook-injector, add to or remove from whitelists]
---

# ack-kubernetes-webhook-injector

In some scenarios where fine-grained permission control is required, you may need to dynamically add the IP addresses of pods to specific whitelists of Alibaba Cloud services. You may also need to remove these IP addresses from specific whitelists of Alibaba Cloud services. You can use ack-kubernetes-webhook-injector to perform these operations. This requires you to add annotations to pod configurations. This topic describes the usage notes of ack-kubernetes-webhook-injector and lists the latest changes to ack-kubernetes-webhook-injector.

## Introduction

ack-kubernetes-webhook-injector is a Kubernetes component that can be used to add pod IP addresses to or remove pod IP addresses from the whitelists of a variety of Alibaba Cloud services. This frees you from manual operations to do this. The following figure shows the architecture of ack-kubernetes-webhook-injector.

![webhook](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4740626261/p283778.png)

## Usage notes

For more information about how to use ack-kubernetes-webhook-injector, see [Use ack-kubernetes-webhook-injector to dynamically add the IP address of a pod to an RDS whitelist or security group](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Infrastructure security/Use ack-kubernetes-webhook-injector to dynamically add the IP address of a pod to an RDS whitelist or security group.md).

## Release notes

**April 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.0.3-d63ac7e|registry-vpc.cn-hangzhou.aliyuncs.com/acs/k8s-webhook-injector:v0.0.3-d63ac7e|2021-04-12|Access control of Server Load Balancer \(SLB\) instances is supported.|No impact on workloads|

