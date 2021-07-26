---
keyword: [Kubernetes volume plug-ins, install Disk Controller]
---

# Install plug-ins

If you specify FlexVolume as the volume plug-in for a Container Service for Kubernetes \(ACK\) cluster that runs Kubernetes earlier than 1.16, the system installs FlexVolume and Disk Controller in the cluster by default. This topic describes how to install the FlexVolume plug-in.

## Prerequisites

-   A serverless Kubernetes \(ASK\) cluster is created. For more information, see [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md).
-   You are connected to a master node of the cluster by using kubectl. For more information, see [Use kubectl to connect to an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Cluster/Manage and access clusters/Use kubectl to connect to an ASK cluster.md).

## Limits

Only CentOS 7 and Aliyun Linux 2 are supported.

## Install the plug-ins

**Install FlexVolume**

-   Clusters that run Kubernetes 1.16 and later do not support FlexVolume. You must install CSI-Plugin in these clusters. For more information, see [Differences between FlexVolume and CSI](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Storage management-CSI/Volume plug-ins.md).
-   If you specify FlexVolume as the volume plug-in for a cluster of ACK earlier than 1.16, the system installs FlexVolume in the cluster by default. For more information, see [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md).

**Install Disk Controller**

-   Clusters of ACK 1.16 and later do not support Disk Controller. You must install CSI-Provisioner in these clusters. For more information, see [Differences between FlexVolume and CSI](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Storage management-Flexvolume/Volume plug-ins.md).
-   If you specify FlexVolume as the volume plug-in for a cluster of ACK earlier than 1.16, the system installs Disk Controller in the cluster by default. For more information, see [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md).

## Verify the installation

You can connect to a master node of your cluster to check whether Disk Controller is installed.

Run the following command. If a list of pods that are in the Running state is returned, it indicates that Disk Controller is installed.

```
kubectl get pod -n kube-system | grep alicloud-disk-controller
```

