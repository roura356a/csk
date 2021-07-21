---
keyword: [kube-flannel-ds, network component used to configure overlay networks for containers]
---

# kube-flannel-ds

kube-flannel-ds is a network component used to configure overlay networks for containers in edge Kubernetes clusters. This topic introduces kube-flannel-ds and lists the latest changes to kube-flannel-ds.

## Introduction

The kube-flannel-ds component is automatically installed when you create an edge Kubernetes cluster. The component is deployed as a DaemonSet and used to configure Virtual Extensible LAN \(VXLAN\) devices, forwarding database \(FDB\) tables, and routing information for cluster nodes. This allows you to enable communication between containers on the same node or different nodes.

## Usage notes

The kube-flannel-ds component is automatically configured in the Container Network Interface \(CNI\) plug-in. You can directly use kube-flannel-ds when you call the Kubernetes API to create applications.

## Release notes

**May 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.11.0.6-1283a29-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/flannel:v0.11.0.6-1283a29-aliyun|May 2021|A startup parameter named enable-cloud-edge-isolate is added. Overlay routing between edge nodes and cloud nodes is supported.|No impact on workloads|

**January 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.11.0.5-437a359-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/flannel:v0.11.0.5-437a359-aliyun|January 2021|None.|No impact on workloads|

