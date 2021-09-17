---
keyword: directx-device-plugin-windows
---

# directx-device-plugin-windows

This topic introduces directx-device-plugin-windows and describes the usage notes and release notes for directx-device-plugin-windows.

## Introduction

directx-device-plugin-windows is a DirectX device plug-in for Container Service for Kubernetes \(ACK\) clusters. directx-device-plugin-windows enables GPU acceleration based on DirectX for Windows containers that are deployed on GPU-accelerated virtualization instances. For more information about GPU-accelerated virtualization instances, see [GPU-accelerated virtualization instance families]().

directx-device-plugin-windows supports only ACK clusters that use Kubernetes 1.20.4 or later.

## Usage notes

By default, directx-device-plugin-windows is deployed as a DaemonSet. directx-device-plugin-windows is reliant on the privileged proxy processes of Windows nodes. directx-device-plugin-windows can be installed only on Windows nodes that are added with the `windows.alibabacloud.com/deployment-topology=2.0` and the `windows.alibabacloud.com/directx-supported=true` labels.

The following section describes the rules for adding the `windows.alibabacloud.com/deployment-topology=2.0` label to Windows nodes:

-   If the Kubernetes version of your cluster is 1.20.4 or later, the `windows.alibabacloud.com/deployment-topology=2.0` label is automatically added to Windows nodes when the Windows nodes are created. If the Windows nodes are not added with the `windows.alibabacloud.com/deployment-topology=2.0` label, you can manually add the label to the Windows nodes.
-   If the Kubernetes version of your cluster is earlier than 1.20.4, the `windows.alibabacloud.com/deployment-topology=2.0` label is not added to Windows nodes when the Windows nodes are created. You cannot manually add the `windows.alibabacloud.com/deployment-topology=2.0` label to the Windows nodes.

The following section describes the rules for adding the `windows.alibabacloud.com/directx-supported=true` label to Windows nodes:

When you create a Windows node pool, the instances that you select must be GPU-accelerated instances. This way, you can add the `windows.alibabacloud.com/directx-supported=true` label to the Windows nodes. If the instances that you are select are GPU-accelerated instances when you create the Windows node pool, the `windows.alibabacloud.com/directx-supported=true` label is automatically added to the Windows nodes. If the Windows nodes are not added with the `windows.alibabacloud.com/directx-supported=true` label, you can manually add the label to the Windows nodes.

directx-device-plugin-windows can only be manually installed. For more information, see [Enable GPU acceleration for DirectX in Windows containers](/intl.en-US/User Guide for Kubernetes Clusters/Windows container/Enable GPU acceleration for DirectX in Windows containers.md).

## Release notes

**August 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.0|registry-vpc.$\{region\}.aliyuncs.com/acs/directx-device-plugin-windows:v1.0.0|2021-08-19|-   The base images are updated. The original version number is overwritten.
-   The following Windows operating systems are supported: Windows Server version 1809 \(10.0.17763.2114\), Windows Server version 1909 \(10.0.18363.1556\), and Windows Server version 2004 \(10.0.19041.1165\).

|No impact on workloads.|

**July 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.0|registry-vpc.$\{region\}.aliyuncs.com/acs/directx-device-plugin-windows:v1.0.0|2021-07-05|The following Windows operating systems are supported: Windows Server version 1809 \(10.0.17763.1999\), Windows Server version 1909 \(10.0.18363.1556\), and Windows Server version 2004 \(10.0.19041.1052\).|No impact on workloads.|

