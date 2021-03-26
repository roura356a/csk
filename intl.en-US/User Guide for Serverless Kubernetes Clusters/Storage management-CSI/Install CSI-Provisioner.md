# Install CSI-Provisioner

CSI-Provisioner can automatically mount disks and Apsara File Storage NAS \(NAS\) file systems as volumes. This topic describes how to install the CSI-Provisioner plug-in in a serverless Kubernetes \(ASK\) cluster.

## Prerequisites

-   [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md)
-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md)

## Install CSI-Provisioner

By default, CSI-Provisioner is installed in ASK clusters. If CSI-Provisioner is not installed in your ASK cluster, you can manually install it. For more information, see [alibaba-cloud-csi-driver](https://github.com/AliyunContainerService/serverless-k8s-examples/blob/master/volumes/csi-provisioner.yaml).

## Verify the installation

You can connect to a master node of your cluster to check whether CSI-Provisioner is installed. For more information about how to connect to a master node, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).

Run the following command. If a list of pods that are in the Running state is returned, it indicates that CSI-Provisioner is installed.

```
kubectl get pod -n kube-system | grep csi-provisioner
```

