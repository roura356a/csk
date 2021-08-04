---
keyword: [directx-device-plugin-windows, GPU acceleration]
---

# directx-device-plugin-windows

This topic introduces directx-device-plugin-windows, describes the usage notes of directx-device-plugin-windows, and lists the latest changes to directx-device-plugin-windows.

## Introduction

directx-device-plugin-windows is a DirectX device plug-in for Container Service for Kubernetes \(ACK\) clusters. directx-device-plugin-windows enables GPU acceleration based on DirectX for Windows containers that are deployed on GPU-accelerated virtualization instances. For more information about GPU-accelerated virtualization instances, see [GPU-accelerated virtualization instance families]().

directx-device-plugin-windows supports only ACK clusters that use Kubernetes 1.20.4 or later.

## Usage notes

directx-device-plugin-windows is deployed as a DaemonSet. directx-device-plugin-windows is dependent on the privileged hosting processes of Windows worker nodes. directx-device-plugin-windows can be installed only on Windows worker nodes that are added from node pools and have the `windows.alibabacloud.com/deployment-topology=2.0` and `windows.alibabacloud.com/directx-supported=true` labels when the nodes are added to the cluster. If the `windows.alibabacloud.com/deployment-topology=2.0` and `windows.alibabacloud.com/directx-supported=true` labels are added to a Windows worker node after the node is created, directx-device-plugin-windows cannot be installed on the node.

directx-device-plugin-windows can only be manually installed. For more information, see [t2092587.md\#]().

## Release notes

**July 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.0|registry-vpc.$\{region\}.aliyuncs.com/acs/directx-device-plugin-windows:v1.0.0|2021-07-05|The following Windows operating systems are supported: Windows Server version 1809 \(10.0.17763.1999\), Windows Server version 1909 \(10.0.18363.1556\), and Windows Server version 2004 \(10.0.19041.1052\).|No impact on workloads|

