---
keyword: [introduce kube-flannel-ds-windows, how to use kube-flannel-ds-windows, kube-flannel-ds-windows release notea]
---

# kube-flannel-ds-windows

This topic introduces kube-flannel-ds-windows, describes how to use kube-flannel-ds-windows, and lists the latest changes to kube-flannel-ds-windows.

## Introduction

kube-flannel-ds-windows is a container network plug-in used by professional Kubernetes clusters. kube-flannel-ds-windows is used to construct a l2bridge network to connect Windows containers.

## Usage notes

If you choose Flannel as the network plug-in when you create a professional Kubernetes cluster, kube-flannel-ds-windows is automatically deployed as a DaemonSet in the cluster. kube-flannel-ds-windows is dependent on the privileged proxy processes on Windows nodes. kube-flannel-ds-windows can be deployed on only Windows nodes with the `window.alibabacloud.com/deployment-topology=2.0` label. By default, the `window.alibabacloud.com/deployment-topology=2.0` label is added to a Windows node when it is created.

If your cluster does not contain Windows nodes or the Windows nodes in your cluster are not added with the `window.alibabacloud.com/deployment-topology=2.0` label, no pod replicas are provisioned for kube-flannel-ds-windows.

**Note:** If no pod replicas are provisioned for kube-flannel-ds-windows on a Windows node with the `window.alibabacloud.com/deployment-topology=2.0` label, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

## Release notes

**May 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.13.1-aliyun.2|registry-vpc.$\{region\}.aliyuncs.com/acs/flannel-windows:v0.13.1-aliyun.2|2021-05-13|-   Container Network Interface \(CNI\) configurations can be automatically shifted when containerd is used as the container runtime.
-   The following Windows systems are supported: Windows Server Release 1809 \(10.0.17763\), Windows Server Release 1909 \(10.0.18363.1440\), and Windows Server Release 2004 \(10.0.19041.867\).

|No impact on workloads|

**April 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.13.1-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/flannel-windows:v0.13.1-aliyun.1|2021-04-22|-   Rancher Wins is automatically deployed on a Windows node with the `window.alibabacloud.com/deployment-topology=2.0` label. You can deploy Flannel on a Windows node by using Rancher Wins.
-   kube-flannel-ds-windows can be used to construct l2bridge and overlay networks. By default, Alibaba Cloud uses l2bridge networks.
-   The following Windows systems are supported: Windows Server Release 1809 \(10.0.17763\), Windows Server Release 1909 \(10.0.18363.1440\), and Windows Server Release 2004 \(10.0.19041.867\).

|No impact on workloads|

