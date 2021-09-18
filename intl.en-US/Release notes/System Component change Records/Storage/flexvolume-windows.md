---
keyword: flexvolume-windows
---

# flexvolume-windows

The flexvolume-windows component is a container storage interface used in Container Service for Kubernetes \(ACK\) clusters. This topic introduces flexvolume-windows and provides usage notes and release notes for the component.

## Introduction

The flexvolume-windows component is a container storage interface that allows you to mount block storage, such as Alibaba Cloud disks, and Apsara File Storage NAS \(NAS\) to Windows containers in ACK clusters.

## Usage notes

The Kubernetes version of the cluster must be v1.16.9 or later. You must select the FlexVolume plug-in as the volume plug-in when you create the cluster.

**Note:** By default, the FlexVolume plug-in is unavailable when you create clusters. You must [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to apply to be added to a whitelist.

You must manually install flexvolume-windows as a DaemonSet in the cluster. For more information, see Step 2: Install FlexVolume on a Windows node of [Mount disks and SMB file systems to Windows pods](/intl.en-US/User Guide for Kubernetes Clusters/Windows container/Mount disks and SMB file systems to Windows pods.md).

## Release notes

**August 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.0|registry-vpc.$\{region\}.aliyuncs.com/acs/flexvolume-windows:v1.0.0|2021-08-23|The following Windows operating systems are supported: Windows Server version 1809 \(10.0.17763.2114\), Windows Server version 1909 \(10.0.18363.1556\), and Windows Server version 2004 \(10.0.19041.1165\).|No impact on workloads|

