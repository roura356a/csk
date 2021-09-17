---
keyword: kube-flannel-ds-windows
---

# kube-flannel-ds-windows

This topic introduces kube-flannel-ds-windows and describes the usage notes and release notes for kube-flannel-ds-windows.

## Introduction

kube-flannel-ds-windows is a container network plug-in used by managed Kubernetes clusters. kube-flannel-ds-windows is used to set up a l2bridge network to connect Windows containers.

## Usage notes

If you choose Flannel as the network plug-in when you create a managed Kubernetes cluster, kube-flannel-ds-windows is automatically deployed as a DaemonSet in the cluster. kube-flannel-ds-windows is dependent on the privileged proxy processes on Windows nodes. kube-flannel-ds-windows can be deployed only on Windows nodes that are added with the `windows.alibabacloud.com/deployment-topology=2.0` label. By default, the `windows.alibabacloud.com/deployment-topology=2.0` label is added to a Windows node when the node is created.

If your cluster does not contain Windows nodes or the Windows nodes in your cluster are not added with the `windows.alibabacloud.com/deployment-topology=2.0` label, no replicated pods are provisioned for kube-flannel-ds-windows.

If a Windows node is added with the `windows.alibabacloud.com/deployment-topology=2.0` label but no replicated pods are provisioned for kube-flannel-ds-windows, you must redeploy kube-flannel-cfg-windows and kube-flannel-ds-windows.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).
2.  In the left-side navigation pane of the ACK console, click **Clusters**.
3.  On the Clusters page, find the cluster that you want to manage and click the name or click **Details** in the **Actions** column.
4.  In the left-side navigation pane, choose **Operations** \> **Add-ons**.
5.  On the **Add-ons** page, find kube-flannel-ds, click ![More Icon](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/2215336261/p283826.png), and then click **View in YAML**.
6.  In the **View in YAML** panel, copy the configurations of kube-flannel-cfg-windows and kube-flannel-ds-windows.
7.  In the left-side navigation pane of the cluster details page, choose **Workloads** \> **Deployments**.
8.  On the **Deployments** page, click **Create from YAML** in the upper-right corner.
9.  Set **Sample Template** to **Custom**. Paste the configurations of kube-flannel-cfg-windows and kube-flannel-ds-windows to the **Template** field. Click **Create**.

## Release notes

**August 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.13.1-aliyun.3|registry-vpc.$\{region\}.aliyuncs.com/acs/flannel-windows:v0.13.1-aliyun.3|2021-08-22|-   The base images are updated. The original version number is overwritten.
-   The following Windows operating systems are supported: Windows Server version 1809 \(10.0.17763.2114\), Windows Server version 1909 \(10.0.18363.1556\), and Windows Server version 2004 \(10.0.19041.1165\).

|No impact on workloads.|

**July 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.13.1-aliyun.3|registry-vpc.$\{region\}.aliyuncs.com/acs/flannel-windows:v0.13.1-aliyun.3|2021-07-05|-   The base images are updated. The original version number is overwritten.
-   The following Windows operating systems are supported: Windows Server version 1809 \(10.0.17763.1999\), Windows Server version 1909 \(10.0.18363.1556\), and Windows Server version 2004 \(10.0.19041.1052\).

|No impact on workloads.|

**June 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.13.1-aliyun.3|registry-vpc.$\{region\}.aliyuncs.com/acs/flannel-windows:v0.13.1-aliyun.3|2021-06-17|-   The base images are updated.
-   The following Windows systems are supported: Windows Server version 1809 \(10.0.17763.1935\), Windows Server version 1909 \(10.0.18363.1556\), and Windows Server version 2004 \(10.0.19041.985\).

|No impact on workloads.|

**May 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.13.1-aliyun.2|registry-vpc.$\{region\}.aliyuncs.com/acs/flannel-windows:v0.13.1-aliyun.2|2021-05-13|-   Container Network Interface \(CNI\) configurations can be automatically shifted when containerd is used as the container runtime.
-   The following Windows operating systems are supported: Windows Server version 1809 \(10.0.17763.1879\), Windows Server version 1909 \(10.0.18363.1500\), and Windows Server version 2004 \(10.0.19041.928\).

|No impact on workloads.|

**April 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.13.1-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/flannel-windows:v0.13.1-aliyun.1|2021-04-22|-   Rancher Wins is automatically installed on a Windows node with the `windows.alibabacloud.com/deployment-topology=2.0` label. You can deploy Flannel on a Windows node by using Rancher Wins.
-   kube-flannel-ds-windows can be used to construct l2bridge and overlay networks. By default, Alibaba Cloud uses l2bridge networks.
-   The following Windows operating systems are supported: Windows Server version 1809 \(10.0.17763.1879\), Windows Server version 1909 \(10.0.18363.1500\), and Windows Server version 2004 \(10.0.19041.928\).

|No impact on workloads.|

