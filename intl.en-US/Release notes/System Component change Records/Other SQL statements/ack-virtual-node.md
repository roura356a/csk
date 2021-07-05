---
keyword: [ack-virtual-node, virtual node component, release notes]
---

# ack-virtual-node

This topic lists the latest changes to ack-virtual-node.

For more information about how to deploy ack-virtual-node in App Catalog, see [Deploy ack-virtual-node in an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy the virtual node controller and use it to create Elastic Container Instance-based pods.md).

## Overview

The ack-virtual-node component is developed based on the open source Virtual Kubelet project and is extended to support Aliyun Provider. Many improvements are made to the ack-virtual-node component to enable seamless integration between Kubernetes and Elastic Container Instance. This way, Kubernetes clusters are empowered with high elasticity and are no longer limited by the computing capacity of cluster nodes. You can dynamically create Elastic Container Instance-based pods to meet your business requirements. This saves the trouble of cluster sizing.

## May 2021

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v2.0.0.122-bdb884460-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.122-bdb884460-aliyun|2021-05-24|The issue that nodes are repeatedly created when concurrent requests are processed is fixed.|No impact on workloads.|
|v2.0.0.113-650b21c48-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.113-650b21c48-aliyun|2021-05-13|Rolling update of PrivateZone is supported.|No impact on workloads.|
|v2.0.0.110-49c928287-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.110-49c928287-aliyun|2021-05-06|NodeSelector can be used to perform pod scheduling. This means that when a pod is configured with the `type=virtual-kubelet` label, the pod is managed by Virtual Kubelet.|No impact on workloads.|

## April 2021

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v2.0.0.102-045a06eb4-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.102-045a06eb4-aliyun|2021-04-22|The issue of displaying nodes that are managed by Virtual Kubelet is fixed.|No impact on workloads.|
|v2.0.0.101-cd8bcfa04-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.101-cd8bcfa04-aliyun|2021-04-16|The Alibaba Cloud DNS PrivateZone API is optimized. This saves you the need to call BindZoneVpc repeatedly.|No impact on workloads.|
|v2.0.0.93-ef86bbfa1-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.93-ef86bbfa1-aliyun|2021-04-13|The value of eciLogtailMacheineGroupKey is modified to support the log collection feature for environment variables and custom resource definitions \(CRDs\).|No impact on workloads.|
|v2.0.0.90-15deb126e-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.90-15deb126e-aliyun|2021-04-06|The feature of Elastic Container Instance Profile is supported.|No impact on workloads.|

## March 2021

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v2.0.0.86-9005a977d-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.86-9005a977d-aliyun|2021-03-17|-   The /etc/hosts file is optimized to support annotations of Domain and DNS.
-   The time required to check ClusterRoleBinding is reduced.
-   Leader elections of multiple components are optimized.

|No impact on workloads.|

## February 2021

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v2.0.0.80-f9a46a994-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.80-f9a46a994-aliyun|2021-02-25|The feature to check the CreatePod state is optimized: A pod can be created only when CreatePod is in the pending state.|No impact on workloads.|
|v2.0.0.76-6e9e19bd5-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.76-6e9e19bd5-aliyun|2021-02-22|-   PProf debugging is supported.
-   Switches are added to control leader elections.
-   ClusterRole is updated for EndPointSlice.
-   Annotations of Kubernetes versions can be added to pods.
-   Different webhooks can be configured in related Kubernetes versions.

|No impact on workloads.|

## December 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v2.0.0.618-7fd50d738-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.618-7fd50d738-aliyun|2020-12-04|-   ack-virtual-node of this version has better compatibility with pods.
-   Multiple leader election logic of the vk controller is supported.

|Make sure that virtual private clouds \(VPCs\) and security groups are correctly configured for pods, and the pods can access the API server of your cluster. Otherwise, the pods remain in the Waiting state.|

## March 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.0.2-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v1.0.0.2-aliyun|2020-03-12|-   The virtual-nodes-eci controller can be deployed on StatefulSets. This allows you to change the number of replicas to create multiple virtual nodes and more pods.
-   The names of virtual nodes use the virtual-node-eci-$n format.
-   Pods are allowed to access ClusterIP Services.
-   Preemptible instances are supported.
-   Disks can be mounted by using the Container Storage Interface \(CSI\).

|If the virtual-nodes-eci controller is deployed on a Deployment, delete the pods on the virtual-kubelet node and install the component again.|

