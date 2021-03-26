# Install CSI

CSI-Provisioner can automatically mount cloud disks and Apsara File Storage NAS \(NAS\) file systems as volumes and persistent volumes \(PVs\). This topic describes how to install CSI-Provisioner in a serverless Kubernetes \(ASK\) cluster.

## Prerequisites

-   [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md)
-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md)

## Install CSI-Provisioner

You need to install CSI-Provisioner in the ASK cluster. If CSI-Provisioner is not installed, you can manually install it in the cluster. For more information, see [alibaba-cloud-csi-driver](https://github.com/AliyunContainerService/serverless-k8s-examples/blob/master/volumes/csi-provisioner.yaml).

## Verify the installation

You can connect to a master node of the cluster to check whether CSI-Provisioner is installed. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).

Run the following command to query the CSI-Provisioner pods. If the output shows that the pods are in the Running state, it indicates that CSI-Provisioner is installed.

```
kubectl get pod -n kube-system | grep csi-provisioner
```

