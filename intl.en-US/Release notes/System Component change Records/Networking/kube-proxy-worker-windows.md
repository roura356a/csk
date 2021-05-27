---
keyword: [introduce kube-proxy-worker-windows, how to use kube-proxy-worker-windows, kube-proxy-worker-windows release notes]
---

# kube-proxy-worker-windows

This topic introduces kube-proxy-worker-windows, describes how to use kube-proxy-worker-windows, and lists the latest changes to kube-proxy-worker-windows.

## Introduction

kube-proxy-worker-windows is a containerized kube-proxy used by professional Kubernetes clusters. kube-proxy-worker-windows manages the endpoints of Services on Windows nodes, including the internal endpoints and external endpoints.

## Usage notes

When the system creates a professional Kubernetes cluster, kube-proxy-worker-windows is automatically deployed as a DaemonSet in the cluster. kube-proxy-worker-windows is dependent on the privileged proxy processes on Windows nodes. kube-proxy-worker-windows can be deployed on only Windows nodes with the `window.alibabacloud.com/deployment-topology=2.0` label. By default, the `window.alibabacloud.com/deployment-topology=2.0` label is added to a Windows node when it is created.

If your cluster does not contain Windows nodes or the Windows nodes in your cluster are not added with the `window.alibabacloud.com/deployment-topology=2.0` label, no pod replicas are provisioned for kube-proxy-worker-windows.

**Note:** If no pod replicas are provisioned for kube-proxy-worker-windows on a Windows node with the `window.alibabacloud.com/deployment-topology=2.0` label, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

## Release notes

**April 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.20.4-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/kube-proxy-windows:v1.20.4-aliyun.1|2021-04-23|-   Rancher Wins is automatically deployed on a Windows node with the `window.alibabacloud.com/deployment-topology=2.0` label. You can deploy kube-proxy 1.20.4-aliyun.1 on a Windows node by using Rancher Wins.
-   The following Windows systems are supported: Windows Server Release 1809 \(10.0.17763\), Windows Server Release 1909 \(10.0.18363.1440\), and Windows Server Release 2004 \(10.0.19041.867\).

|No impact on workloads|
|v1.18.8-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/kube-proxy-windows:v1.18.8-aliyun.1|2021-04-23|-   Rancher Wins is automatically deployed on a Windows node with the `window.alibabacloud.com/deployment-topology=2.0` label. You can deploy kube-proxy 1.18.8-aliyun.1 on a Windows node by using Rancher Wins.
-   The following Windows systems are supported: Windows Server Release 1809 \(10.0.17763\), Windows Server Release 1909 \(10.0.18363.1440\), and Windows Server Release 2004 \(10.0.19041.867\).

|No impact on workloads|
|v1.16.9-aliyun.1|registry-vpc.$\{region\}.aliyuncs.com/acs/kube-proxy-windows:v1.16.9-aliyun.1|2021-04-23|-   kube-proxy-worker-windows is automatically deployed on a Windows node with the `window.alibabacloud.com/deployment-topology=2.0` label. You can deploy kube-proxy 1.16.9-aliyun.1 on a Windows node by using kube-proxy-worker-windows.
-   The following Windows systems are supported: Windows Server Release 1809 \(10.0.17763\), Windows Server Release 1909 \(10.0.18363.1440\), and Windows Server Release 2004 \(10.0.19041.867\).

|No impact on workloads|

