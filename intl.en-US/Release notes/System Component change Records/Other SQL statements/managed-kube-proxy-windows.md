---
keyword: [introduction to managed-kube-proxy-windows, how to use managed-kube-proxy-windows, managed-kube-proxy-windows release notes]
---

# managed-kube-proxy-windows

This topic introduces managed-kube-proxy-windows, describes how to use managed-kube-proxy-windows, and lists the latest changes to managed-kube-proxy-windows.

## Introduction

managed-kube-proxy-windows is a containerized kube-proxy used by professional Kubernetes clusters. managed-kube-proxy-windows manages the endpoints of Services on Windows nodes, including internal endpoints and external endpoints.

## Usage notes

When the system creates a professional Kubernetes cluster, managed-kube-proxy-windows is automatically deployed as a DaemonSet in the cluster. managed-kube-proxy-windows is dependent on the privileged proxy processes on Windows nodes. managed-kube-proxy-windows can be deployed only on Windows nodes that are added with the `window.alibabacloud.com/deployment-topology=2.0` label. By default, the `window.alibabacloud.com/deployment-topology=2.0` label is added to a Windows node when the node is created.

If your cluster does not contain Windows nodes or the Windows nodes in your cluster are not added with the `window.alibabacloud.com/deployment-topology=2.0` label, no pod replicas are provisioned for managed-kube-proxy-windows.

If no pod replicas are provisioned for managed-kube-proxy-windows on a Windows node that is added with the `window.alibabacloud.com/deployment-topology=2.0` label, you must manually install managed-kube-proxy-windows.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).
2.  In the left-side navigation pane of the ACK console, click **Clusters**.
3.  On the Clusters page, find the cluster that you want to manage and click the name or click **Details** in the **Actions** column.
4.  In the left-side navigation pane of the cluster details page, choose **Operations** \> **Add-ons**.
5.  On the **Add-ons** page, find managed-kube-proxy-windows and click **Install**.
6.  In the message that appears, click **OK**.

## Release notes

**June 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.20.4-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/kube-proxy-windows:v1.20.4-aliyun.1|2021-06-17|-   The base images are updated. The original version number is overwritten.
-   The following Windows operating systems are supported: Windows Server version 1809 \(10.0.17763.1935\), Windows Server version 1909 \(10.0.18363.1556\), and Windows Server version 2004 \(10.0.19041.985\).

|No impact on workloads|
|v1.18.8-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/kube-proxy-windows:v1.18.8-aliyun.1|2021-06-17|-   The base images are updated. The original version number is overwritten.
-   The following Windows operating systems are supported: Windows Server version 1809 \(10.0.17763.1935\), Windows Server version 1909 \(10.0.18363.1556\), and Windows Server version 2004 \(10.0.19041.985\).

|No impact on workloads|
|v1.16.9-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/kube-proxy-windows:v1.16.9-aliyun.1|2021-06-17|-   The base images are updated. The original version number is overwritten.
-   The following Windows operating systems are supported: Windows Server version 1809 \(10.0.17763.1935\), Windows Server version 1909 \(10.0.18363.1556\), and Windows Server version 2004 \(10.0.19041.985\).

|No impact on workloads|

**April 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.20.4-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/kube-proxy-windows:v1.20.4-aliyun.1|2021-04-23|-   Rancher Wins is automatically installed on a Windows node with the `window.alibabacloud.com/deployment-topology=2.0` label. You can deploy kube-proxy 1.20.4-aliyun.1 on a Windows node by using Rancher Wins.
-   The following Windows operating systems are supported: Windows Server version 1809 \(10.0.17763\), Windows Server version 1909 \(10.0.18363.1440\), and Windows Server version 2004 \(10.0.19041.867\).

|No impact on workloads|
|v1.18.8-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/kube-proxy-windows:v1.18.8-aliyun.1|2021-04-23|-   Rancher Wins is automatically installed on a Windows node with the `window.alibabacloud.com/deployment-topology=2.0` label. You can deploy kube-proxy 1.18.8-aliyun.1 on a Windows node by using Rancher Wins.
-   The following Windows operating systems are supported: Windows Server version 1809 \(10.0.17763\), Windows Server version 1909 \(10.0.18363.1440\), and Windows Server version 2004 \(10.0.19041.867\).

|No impact on workloads|
|v1.16.9-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/kube-proxy-windows:v1.16.9-aliyun.1|2021-04-23|-   Rancher Wins is automatically installed on a Windows node with the `window.alibabacloud.com/deployment-topology=2.0` label. You can deploy kube-proxy 1.16.9-aliyun.1 on a Windows node by using Rancher Wins.
-   The following Windows operating systems are supported: Windows Server version 1809 \(10.0.17763\), Windows Server version 1909 \(10.0.18363.1440\), and Windows Server version 2004 \(10.0.19041.867\).

|No impact on workloads|

