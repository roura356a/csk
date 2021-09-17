---
keyword: managed-kube-proxy-windows
---

# managed-kube-proxy-windows

This topic introduces managed-kube-proxy-windows and describes the usage notes and release notes for managed-kube-proxy-windows.

## Introduction

managed-kube-proxy-windows is a containerized kube-proxy used by managed Kubernetes clusters. managed-kube-proxy-windows manages the endpoints of Services on Windows nodes, including internal endpoints and external endpoints.

## Usage notes

When the system creates a managed Kubernetes cluster, managed-kube-proxy-windows is automatically deployed as a DaemonSet in the cluster. managed-kube-proxy-windows is reliant on the privileged proxy processes on Windows nodes. managed-kube-proxy-windows can be deployed only on Windows nodes that are added with the `windows.alibabacloud.com/deployment-topology=2.0` label. The following section describes the rules for adding the `windows.alibabacloud.com/deployment-topology=2.0` label to Windows nodes:

-   If the Kubernetes version of your cluster is 1.20.4 or later, the `windows.alibabacloud.com/deployment-topology=2.0` label is automatically added to Windows nodes when you create the Windows nodes. If the label is not added to the Windows nodes, you can manually add the `windows.alibabacloud.com/deployment-topology=2.0` label to the Windows nodes.
-   If the Kubernetes version of your cluster is earlier than 1.20.4, the `windows.alibabacloud.com/deployment-topology=2.0` label is not added to Windows nodes when you create the Window nodes. You cannot manually add the `windows.alibabacloud.com/deployment-topology=2.0` label to the Windows nodes.

If your cluster does not contain Windows nodes or the Windows nodes in your cluster do not have the `windows.alibabacloud.com/deployment-topology=2.0` label, no replicated pods are provisioned for managed-kube-proxy-windows.

If no replicated pods are provisioned for managed-kube-proxy-windows on a Windows node with the `windows.alibabacloud.com/deployment-topology=2.0` label, you must manually install managed-kube-proxy-windows.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).
2.  In the left-side navigation pane of the ACK console, click **Clusters**.
3.  On the Clusters page, find the cluster that you want to manage and click the name or click **Details** in the **Actions** column.
4.  In the left-side navigation pane, choose **Operations** \> **Add-ons**.
5.  On the **Add-ons** page, find managed-kube-proxy-windows and click **Install**.
6.  In the message that appears, click **OK**.

## Release notes

**August 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.20.4-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/kube-proxy-windows:v1.20.4-aliyun.1|2021-08-22|-   The base images are updated. The original version numbers are overwritten.
-   The following Windows operating systems are supported: Windows Server version 1809 \(10.0.17763.2114\), Windows Server version 1909 \(10.0.18363.1556\), and Windows Server version 2004 \(10.0.19041.1165\).

|No impact on workloads.|
|v1.18.8-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/kube-proxy-windows:v1.18.8-aliyun.1|2021-08-20|-   The base images are updated. The original version numbers are overwritten.
-   The following Windows operating systems are supported: Windows Server version 1809 \(10.0.17763.2114\), Windows Server version 1909 \(10.0.18363.1556\), and Windows Server version 2004 \(10.0.19041.1165\).

|No impact on workloads.|
|v1.16.9-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/kube-proxy-windows:v1.16.9-aliyun.1|2021-08-20|-   The base images are updated. The original version numbers are overwritten.
-   The following Windows operating systems are supported: Windows Server version 1809 \(10.0.17763.2114\), Windows Server version 1909 \(10.0.18363.1556\), and Windows Server version 2004 \(10.0.19041.1165\).

|No impact on workloads.|

**July 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.20.4-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/kube-proxy-windows:v1.20.4-aliyun.1|2021-07-05|-   The base images are updated. The original version numbers are overwritten.
-   The following Windows operating systems are supported: Windows Server version 1809 \(10.0.17763.1999\), Windows Server version 1909 \(10.0.18363.1556\), and Windows Server version 2004 \(10.0.19041.1052\).

|No impact on workloads.|
|v1.18.8-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/kube-proxy-windows:v1.18.8-aliyun.1|2021-07-05|-   The base images are updated. The original version numbers are overwritten.
-   The following Windows operating systems are supported: Windows Server version 1809 \(10.0.17763.1999\), Windows Server version 1909 \(10.0.18363.1556\), and Windows Server version 2004 \(10.0.19041.1052\).

|No impact on workloads.|
|v1.16.9-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/kube-proxy-windows:v1.16.9-aliyun.1|2021-07-05|-   The base images are updated. The original version numbers are overwritten.
-   The following Windows operating systems are supported: Windows Server version 1809 \(10.0.17763.1999\), Windows Server version 1909 \(10.0.18363.1556\), and Windows Server version 2004 \(10.0.19041.1052\).

|No impact on workloads.|

**June 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.20.4-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/kube-proxy-windows:v1.20.4-aliyun.1|2021-06-17|-   The base images are updated. The original version numbers are overwritten.
-   The following Windows systems are supported: Windows Server version 1809 \(10.0.17763.1935\), Windows Server version 1909 \(10.0.18363.1556\), and Windows Server version 2004 \(10.0.19041.985\).

|No impact on workloads.|
|v1.18.8-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/kube-proxy-windows:v1.18.8-aliyun.1|2021-06-17|-   The base images are updated. The original version numbers are overwritten.
-   The following Windows systems are supported: Windows Server version 1809 \(10.0.17763.1935\), Windows Server version 1909 \(10.0.18363.1556\), and Windows Server version 2004 \(10.0.19041.985\).

|No impact on workloads.|
|v1.16.9-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/kube-proxy-windows:v1.16.9-aliyun.1|2021-06-17|-   The base images are updated. The original version numbers are overwritten.
-   The following Windows systems are supported: Windows Server version 1809 \(10.0.17763.1935\), Windows Server version 1909 \(10.0.18363.1556\), and Windows Server version 2004 \(10.0.19041.985\).

|No impact on workloads.|

**April 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.20.4-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/kube-proxy-windows:v1.20.4-aliyun.1|2021-04-23|-   Rancher Wins is automatically installed on a Windows node with the `windows.alibabacloud.com/deployment-topology=2.0` label. You can deploy kube-proxy 1.20.4-aliyun.1 on a Windows node by using Rancher Wins.
-   The following Windows operating systems are supported: Windows Server version 1809 \(10.0.17763\), Windows Server version 1909 \(10.0.18363.1440\), and Windows Server version 2004 \(10.0.19041.867\).

|No impact on workloads.|
|v1.18.8-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/kube-proxy-windows:v1.18.8-aliyun.1|2021-04-23|-   Rancher Wins is automatically installed on a Windows node with the `windows.alibabacloud.com/deployment-topology=2.0` label. You can deploy kube-proxy 1.18.8-aliyun.1 on a Windows node by using Rancher Wins.
-   The following Windows operating systems are supported: Windows Server version 1809 \(10.0.17763\), Windows Server version 1909 \(10.0.18363.1440\), and Windows Server version 2004 \(10.0.19041.867\).

|No impact on workloads.|
|v1.16.9-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/kube-proxy-windows:v1.16.9-aliyun.1|2021-04-23|-   Rancher Wins is automatically installed on a Windows node with the `windows.alibabacloud.com/deployment-topology=2.0` label. You can deploy kube-proxy v1.16.9-aliyun.1 on a Windows node by using Rancher Wins. The following Windows operating systems are supported: Windows Server version 1809 \(10.0.17763\), Windows Server version 1909 \(10.0.18363.1440\), and Windows Server 2004 \(10.0.19041.867\).
-   
|No impact on workloads.|

