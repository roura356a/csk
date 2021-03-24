---
keyword: [ack-virtual-node, virtual node component, release notes]
---

# ack-virtual-node

This topic lists the latest changes to ack-virtual-node.

For more information about how to deploy ack-virtual-node in App Catalog, see [Deploy ack-virtual-node in an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy ack-virtual-node in an ACK cluster.md).

## Version v2.0.0.618-7fd50d738-aliyun \(December 4, 2020\)

|Version|Image address|Description|Impact|
|-------|-------------|-----------|------|
|v2.0.0.618-7fd50d738-aliyun|registry-vpc. $RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.618-7fd50d738-aliyun|-   ack-virtual-node of this version has better compatibility with pods.
-   The version supports the multiple leader election logic of the vk controller.

|Make sure that virtual private clouds \(VPCs\) and security groups are correctly configured for pods, and the pods can access the API Server of your cluster. Otherwise, the pods remain in the Waiting state.|

## Version v1.0.0.2-aliyun \(March 12, 2020\)

|Version|Image address|Description|Impact|
|-------|-------------|-----------|------|
|v1.0.0.2-aliyun|registry-vpc. $RegionId.aliyuncs.com/acs/virtual-nodes-eci:v1.0.0.2-aliyun|-   The deployment of the virtual-nodes-eci controller for StatefulSets is supported. This allows you to change the number of replicas. You can also create multiple virtual nodes for more pods.
-   The names of virtual nodes use the virtual-node-eci-$n format.
-   Pods are allowed to access ClusterIP services.
-   Preemptible instances are supported.
-   Disks can be mounted by using the Container Storage Interface \(CSI\).

|If the virtual-nodes-eci controller component is installed for a deployment, delete the pods on the node virtual-kubelet and install the component again.|

