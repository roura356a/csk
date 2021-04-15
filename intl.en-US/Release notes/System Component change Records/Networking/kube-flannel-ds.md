---
keyword: [kube-flannel-ds, network plug-in used to configure overlay networks for containers]
---

# kube-flannel-ds

kube-flannel-ds is a network plug-in used to configure overlay networks for containers in edge Kubernetes clusters. This topic lists the latest changes to kube-flannel-ds.

## Introduction

kube-flannel-ds is the default network plug-in of edge Kubernetes clusters. It is deployed as a DaemonSet. You can configure Virtual Extensible LAN \(VXLAN\) devices, forwarding database \(FDB\) tables, and routing information for nodes in an edge Kubernetes cluster. This allows you to enable communication between containers on nodes of the same type.

## Instruction

You do not need to be concerned about how to use kube-flannel-ds. You can directly specify network configurations when you create applications by calling the Kubernetes API.

## Release notes

**January 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.11.0.5-437a359-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/flannel:v0.11.0.5-437a359-aliyun|2021-01-14|Containers on different types of nodes cannot communicate with each other. For example, containers on edge nodes cannot communicate with containers on nodes in the cloud.|N/A|

