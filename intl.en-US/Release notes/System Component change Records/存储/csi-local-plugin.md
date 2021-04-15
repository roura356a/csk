---
keyword: [csi-local-plugin, CSI plug-in for provisioning on-premises storage]
---

# csi-local-plugin

csi-local-plugin is a Container Storage Interface \(CSI\) plug-in and is used to provision on-premises storage in edge Kubernetes clusters. This topic lists the latest changes to csi-local-plugin.

## Introduction

csi-local-plugin is developed on top of the CSI drivers of Kubernetes. csi-local-plugin allows you to manage on-premises storage resources by using Logical Volume Manager \(LVM\). You can create, delete, mount, and unmount on-premises volumes by using persistent volumes \(PVs\) and persistent volume claims \(PVCs\).

## Instruction

csi-local-plugin is developed on top of the standard CSI driver. It allows you to create PVCs by calling the Kubernetes API and specifying them in pod configurations.

## Release notes

**March 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.16.9-a9145c7-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/csi-plugin:v1.16.9-a9145c7-aliyun|2021-03-24|csi-local-plugin is released for the first time.|N/A|

