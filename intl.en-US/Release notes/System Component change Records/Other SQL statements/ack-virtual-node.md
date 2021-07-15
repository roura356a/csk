---
keyword: [ack-virtual-node, virtual node component, release notes]
---

# ack-virtual-node

This topic lists the latest changes to ack-virtual-node.

For more information about how to deploy ack-virtual-node in App Catalog, see [Deploy ack-virtual-node in an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy the virtual node controller and use it to create Elastic Container Instance-based pods.md).

## Introduction

The ack-virtual-node component is developed based on the open source project Virtual Kubelet and is extended to support Aliyun Provider. Many improvements are made to the ack-virtual-node component to enable seamless integration between Kubernetes and Elastic Container Instance. This way, Kubernetes clusters are empowered with high elasticity and are no longer limited by the computing capacity of cluster nodes. You can dynamically create Elastic Container Instance-based pods to meet your business requirements. This saves the trouble of cluster sizing.

The ack-virtual-node component can be used to run pods as serverless container groups in Elastic Container Instance. The pay-as-you-go billing method is supported. This ensures optimal auto scaling capability and avoids unnecessary resource usage. You can use ack-virtual-node to improve the efficiency of application deployment and auto scaling, and minimize the computing cost in multiple scenarios. These scenarios include Job tasks, continuous integration and continuous delivery \(CI/CD\), Apache Spark big data computing, and online application auto scaling.

For more information about Elastic Container Instance-based pods, see [Elastic Container Instance overview](/intl.en-US/User Guide for Serverless Kubernetes Clusters/ECI Pod/Elastic Container Instance overview.md).

![vk](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7166194061/p168908.png)

## Usage notes

Use ack-virtual-node to create a pod.

1.  Configure permissions.

    Before you can install the component in a registered cluster, you must specify the AccessKey information to authenticate requests that are sent to cloud resources. Before you set the AccessKey information, create a Resource Access Management \(RAM\) user and grant the RAM user the permissions to access Alibaba Cloud resources.

    1.  Create a RAM user.

        For more information, see [Create a RAM user](/intl.en-US/RAM User Management/Basic operations/Create a RAM user.md).

    2.  Create a permission policy.

        For more information, see [Create a custom policy](/intl.en-US/Policy Management/Custom policies/Create a custom policy.md).

        The following template provides an example of the permission policy for ack-virtual-node:

        ```
        {
            "Version": "1",
            "Statement": [
                {
                    "Action": [
                        "eci:CreateContainerGroup",
                        "eci:DeleteContainerGroup",
                        "eci:DescribeContainerGroups",
                        "eci:DescribeContainerLog",
                        "eci:UpdateContainerGroup",
                        "eci:UpdateContainerGroupByTemplate",
                        "eci:CreateContainerGroupFromTemplate",
                        "eci:RestartContainerGroup",
                        "eci:ExportContainerGroupTemplate",
                        "eci:DescribeContainerGroupMetric",
                        "eci:DescribeMultiContainerGroupMetric",
                        "eci:ExecContainerCommand",
                        "eci:CreateImageCache",
                        "eci:DescribeImageCaches",
                        "eci:DeleteImageCache"
                    ],
                    "Resource": [
                        "*"
                    ],
                    "Effect": "Allow"
                }
            ]
        }
        ```

    3.  Grant permissions to the RAM user

        For more information, see [Grant permissions to a RAM user](/intl.en-US/RAM User Management/Authorization management/Grant permissions to a RAM user.md).

    4.  Create an AccessKey pair for the RAM user.

        For more information about how to create an AccessKey pair for the RAM user, see [Obtain an AccessKey pair]().

        Run the following command to create a Secret that is used by ack-virtual-node:

        ```
        kubectl create nskubectl -n kube-system create secret generic alibaba-addon-secret --from-literal='access-key-id=<your access key id>' --from-literal='access-key-secret=<your access key secret>'
        ```

        Replace `<your access key id>` and `<your access key secret>` in the command with your AccessKey ID and AccessKey secret.

2.  Install ack-virtual-node.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  On the Clusters page, find the cluster that you want to manage and choose **More** \> **Manage System Components** in the **Actions** column.

    3.  Find **ack-virtual-node** and click **Install**.

3.  Create an Elastic Container Instance-based pod.

    You can use ack-virtual-node to create an Elastic Container Instance-based pod in the following ways:

    -   Add the label eci=true to the pod.

        ```
        kubectl run nginx --image nginx -l eci=true
        ```

    -   Add the label eci=true to the namespace where the pod is to be deployed.

        ```
        kubectl create ns vk
        
        kubectl label namespace vk eci=true
        
        kubectl -n vk run nginx --image nginx
        ```


## Release notes

**May 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v2.0.0.122-bdb884460-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.122-bdb884460-aliyun|2021-05-24|The issue that nodes are repeatedly created when concurrent requests are processed is fixed.|No impact on workloads|
|v2.0.0.113-650b21c48-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.113-650b21c48-aliyun|2021-05-13|Rolling update of PrivateZone is supported.|No impact on workloads|
|v2.0.0.110-49c928287-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.110-49c928287-aliyun|2021-05-06|Node selectors can be used to configure pod scheduling. This means that when a pod is configured with the `type=virtual-kubelet` label, the pod is managed by Virtual Kubelet.|No impact on workloads|

**April 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v2.0.0.102-045a06eb4-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.102-045a06eb4-aliyun|2021-04-22|The issue that nodes managed by Virtual Kubelet is not correctly displayed is fixed.|No impact on workloads|
|v2.0.0.101-cd8bcfa04-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.101-cd8bcfa04-aliyun|2021-04-16|The calling of the Alibaba Cloud DNS PrivateZone API is optimized. This avoids repeated calls to BindZoneVpc.|No impact on workloads|
|v2.0.0.93-ef86bbfa1-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.93-ef86bbfa1-aliyun|2021-04-13|The value of eciLogtailMacheineGroupKey is modified to support the log collection feature of environment variables and CustomResourceDefinitions \(CRDs\).|No impact on workloads|
|v2.0.0.90-15deb126e-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.90-15deb126e-aliyun|2021-04-06|The feature of Elastic Container Instance Profile is supported.|No impact on workloads|

**March 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v2.0.0.86-9005a977d-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.86-9005a977d-aliyun|2021-03-17|-   The /etc/hosts file is optimized to support the Domain and DNS annotations.
-   The time required to check ClusterRoleBinding is reduced.
-   Leader elections for multiple components are optimized.

|No impact on workloads|

**February 2021**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v2.0.0.80-f9a46a994-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.80-f9a46a994-aliyun|2021-02-25|The feature of checking the CreatePod state is optimized: A pod can be created only when CreatePod is in the Pending state.|No impact on workloads|
|v2.0.0.76-6e9e19bd5-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.76-6e9e19bd5-aliyun|2021-02-22|-   PProf debugging is supported.
-   Switches are added to control leader elections.
-   ClusterRole is updated for EndPointSlice.
-   Annotations of Kubernetes versions can be added to pods.
-   Different webhooks can be configured in related Kubernetes versions.

|No impact on workloads|

**December 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v2.0.0.618-7fd50d738-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v2.0.0.618-7fd50d738-aliyun|2020-12-04|-   Compatibility with pods is improved.
-   Multiple leader election logic of the Virtual Kubelet controller is supported.

|Make sure that virtual private clouds \(VPCs\) and security groups are correctly configured for pods, and the pods can access the API server of your cluster. Otherwise, the pods remain in the waiting state.|

**September 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.0.10-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/virtual-nodes-eci:v1.0.0.10-aliyun|2020-09-21|Auto scaling of clusters is supported. Pods are run as serverless container groups on Elastic Container Instance.|No impact on workloads|

**March 2020**

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v1.0.0.2-aliyun|registry-vpc.$RegionId.aliyuncs.com/acs/virtual-nodes-eci:v1.0.0.2-aliyun|2020-03-12|-   The virtual-nodes-eci controller can be deployed on StatefulSets. This allows you to change the number of pod replicas to create multiple virtual nodes and more pods.
-   The names of virtual nodes use the virtual-node-eci-$n format.
-   Pods are allowed to access ClusterIP Services.
-   Preemptible instances are supported.
-   Disks can be mounted by using the Container Storage Interface \(CSI\) plug-in.

|If the virtual-nodes-eci controller is deployed on a Deployment, delete the pods on the virtual-kubelet node and install the component again.|

