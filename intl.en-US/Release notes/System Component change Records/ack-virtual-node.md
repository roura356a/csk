---
keyword: [ack-virtual-node, virtual node component, release notes]
---

# ack-virtual-node

This topic lists the latest changes to ack-virtual-node.

For more information about how to deploy ack-virtual-node in App Catalog, see [Virtual nodes](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy ack-virtual-node in an ACK cluster.md).

## March 12, 2020

|Version|Image address|Description|Impact|
|-------|-------------|-----------|------|
|v1.0.0.2-aliyun|registry-vpc. $RegionId.aliyuncs.com/acs/virtual-nodes-eci:v1.0.0.2-aliyun|-   The deployment of the virtual-nodes-eci controller for StatefulSets is supported. This allows you to change the number of replicas. You can also create multiple virtual nodes and more pods.
-   The names of virtual nodes follow the virtual-node-eci-$n format.
-   Pods are allowed to access ClusterIP services.
-   Pods that are based on spot pricing are supported.
-   Disks can be mounted based on the Container Storage Interface \(CSI\).

|If the virtual-nodes-eci controller component is installed for a deployment, delete the pods on the node virtual-kubelet and install the component again.|

