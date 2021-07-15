---
keyword: [Flannel, VPC, Kubernetes clusters]
---

# Flannel

Flannel is a Container Network Interface \(CNI\) plug-in that you can use to create a virtual network for containers based on Virtual Private Cloud \(VPC\). You can use Flannel to enable internal communication in a Kubernetes cluster. This topic lists the latest changes to Flannel.

## Introduction

The Flannel network plug-in provided by Container Service for Kubernetes \(ACK\) allocates pod CIDR blocks that are independent of the VPC CIDR block. In an ACK cluster, the Flannel network plug-in works with the VPC in which the cluster is deployed. Packets are directly forwarded based on the VPC route table. This improves communication efficiency because no tunneling technologies such as Virtual Extensible Local Area Network \(VXLAN\) are required to encapsulate the packets. For more information, see [Overview](/intl.en-US/User Guide for Kubernetes Clusters/Network/Overview.md).

## Usage notes

Flannel is an open source CNI plug-in, which is simple and stable. You can use Flannel together with VPC. This way, your clusters and containers can run in high-performance and stable networks. For more information, see [Flannel](https://github.com/flannel-io/flannel?spm=a2c4g.11186623.2.10.28b229747WScYz).

## Release notes

**May 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.13.0.1-466064b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/flannel:v0.13.0.1-466064b-aliyun|2021-05-24|-   Support for iptables commands is optimized to enable compatibility with CentOS 8, and Alibaba Cloud Linux 3 and later.
-   Proactive checks and corrections of IP forwarding configurations are supported.

|No impact on workloads|

**August 2019**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.11.0.2-g6e46593e-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/flannel:v0.11.0.2-g6e46593e-aliyun|2019-08-02|The issue that source IP addresses are not retained after Flannel is upgraded is fixed.|No impact on workloads|

