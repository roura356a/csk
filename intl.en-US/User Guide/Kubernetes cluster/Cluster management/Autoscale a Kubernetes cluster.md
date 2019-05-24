# Autoscale a Kubernetes cluster {#concept_bpt_qvm_z2b .concept}

This topic describes how to autoscale a Kubernetes cluster to meet the requirements of your Kubernetes cluster workload. Alibaba Cloud Container Service for Kubernetes \(ACK\) provides the capability to autoscale a Kubernetes cluster through using the cluster autoscaler program.

## Background information {#section_oh1_p29_ngr .section}

You can set the the cluster autoscaler to add different ECS instance types to your Kubernetes cluster, such as the general, GPU, and preemptive instance types. You can set multiple zones, instance specifications, and autoscaling modes.

## Cluster autoscaler overview {#section_k6p_8sf_1ut .section}

The cluster autoscaler changes the size of a Kubernetes cluster based on the use of resource in the nodes of a pod in a Kubernetes cluster. Resource usage is calculated based on the pod resource requests.

When a pod requests more resources than what the associated node can provide, the pod enters the pending status. At which time, the autoscaler calculates the change to the size of cluster. It does so by calculating the number of nodes necessary to provide the requested resource with regard to the resource specification and threshold that you set for an autoscaling group.

For example, if you set a low threshold value for the number of nodes in an autoscaling group, the cluster autoscaler deletes a node, which reduces the amount of resources that the pod can request.

## Notes {#section_v0t_crm_bvt .section}

-   By default, your account can use up to 30 Pay-As-You-Go ECS instances in all your clusters, and the route table of one VPC can contain up to 50 entries. To increase the number of available ECS instances or entries in a route table of one VPC, open a ticket.
-   For a single type of ECS instances, the number of ECS instances of one specification that is permitted at one time varies frequently. Therefore, we recommend that you set multiple ECS instance types of one ECS specification.
-   When a node for which you set the fast scaling mode is shut down and reclaimed, it is in the NotReady status. When the node is reused by the cluster autoscaler, the node enters the Ready status.
-   When a node for which you set the fast scaling mode is shut down and reclaimed, only the disks attached to the node are charged \(except that the node uses local disks, for example, ecs.d1ne.2xlarge\).

## Enable cluster autoscaling {#section_f5l_qkt_3lm .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Clusters** \> **Clusters**.
3.  Find the target cluster. Then, in the **Action** column, choose **More** \> **Auto Scaling**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/155866344111191_en-US.png)


## **Grant required permissions for the Auto Scaling service and the cluster** {#section_y5u_sec_5il .section}

-   **Activate the Auto Scaling service** 

1.  Click **ESS** in the displayed dialog box.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/155866344111211_en-US.png)

2.  Click **Activate Auto Scaling service**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/155866344111263_en-US.png)

3.  Read and confirm that you agree to the conditions by selecting the **I agree with Auto Scaling Service Agreement of Service** check box, and then click **Enable Now**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/155866344111264_en-US.png)

4.  Click **Console**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/155866344111265_en-US.png)

5.  Click **Authorize**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/155866344111266_en-US.png)

6.  Click **Confirm Authorization Policy** to grant ESS the permission to access your cloud resources.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/155866344111267_en-US.png)

    **Verify the result**

    If the page automatically redirects to the Auto Scaling console, the activation is successful.

-   **Add ESS authorization policies to the cluster** 

1.  Click the Worker RAM role \(`KubernetesWorkerRole[xxx]` \) in the following dialog box.

    **Note:** You need to use the primary account to log on to the console before perform this operation.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/155866344111261_en-US.png)

2.  Click **View Permissions** on the right of the target authorization policy.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/155866344111193_en-US.png)

3.  In the upper-right corner of the page, click **Modify Authorization Policy**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/155866344111195_en-US.png)

4.  In the `Action` field of the **Policy Document** area, add the following policies:

    ``` {#codeblock_d9a_shd_g8h}
    
    "ess:Describe*", 
    "ess:CreateScalingRule", 
    "ess:ModifyScalingGroup", 
    "ess:RemoveInstances", 
    "ess:ExecuteScalingRule", 
    "ess:ModifyScalingRule", 
    "ess:DeleteScalingRule", 
    "ecs:DescribeInstanceTypes",
    "ess:DetachInstances"
    ```

    **Note:** You must add a comma \(,\) to the end of the last line in the `Action` field before adding these policies.

5.  Click **Modify Authorization**.

## Set cluster auto scaling parameters {#section_4jc_wgz_m7r .section}

1.  On the Automatic Scaling page, set the following parameters.

    |Configuration|Description|
    |-------------|-----------|
    |Cluster|Target cluster name.|
    |Scale-in Threshold|The ratio of the amount of resources requested by the cluster workload to the amount of cluster resources. When the amount of resources requested by the cluster workload is less than or equal to the threshold, the system automatically scales in the cluster. The default value is 50%.|
    |Defer Scale-in For|The number of minutes for which the system must wait to automatically scale in the cluster after the scale-in threshold is reached. The default value is 10 minutes.|
    |Cool-Down Time|The period \(in minutes\) during which the system does not automatically scale in or scale out a cluster after the number of cluster nodes increases or decreases. The default is 10 minutes.|

2.  Click **Create** on the right of the target type of resource \(which can be CPU or GPU\) that you want to autoscale.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/155866344111210_en-US.png)

    On the Scaling Group Configuration page, set the following parameters to create a scaling group:

    |Configuration|Description|
    |-------------|-----------|
    |Region|The region to which the scaling group is deployed. You must ensure that the scaling group and the cluster where it is located share the same region. This region cannot be modified.|
    |Zone|The zone where the scaling group is created.|
    |VPC|The network where the scaling group is created. You must ensure that the scaling group and the cluster where it is located are in the same region.|

    Set worker nodes.

    |Configuration|Description|
    |-------------|-----------|
    |Instance Type|Set the specifications of instances in the scaling group.|
    |System Disk|Set the system disks of the scaling group.|
    |Attach Data Disk|Mount a data disk when you create a scaling group. By default, no data disk is mounted.|
    |Instance Quantity|Set the number of instances in the scaling group. **Note:** 

    -   The number does not include the existing instances in the cluster.
    -   By default, this parameter value is 0, and the cluster adds instances to the scaling group and the Kubernetes cluster where the scaling group is located when this parameter exceeds 0.
 |
    |Key Pair|Set the key pair used to log on to the node added through autoscaling. You can create a new key pair in the Elastic Compute Service \(ECS\) console. **Note:** Only key pair logon is supported.

 |
    |RDS Whitelist|Set the Relational Database Service \(RDS\) instances that can be accessed by the node added through autoscaling.|

3.  Click **OK**.

## Verify the result {#section_1ej_w4o_b1n .section}

-   You can directly verify that a scaling group under CPU is displayed on the Auto Scaling page.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/155866344211273_en-US.png)

-   To verify the created autoscaling component, follow these steps:

    1.  In the left-side navigation pane, choose **Application** \> **Deployment**.
    2.  Select the target cluster and the kube-system namespace to view the created component named cluster-autoscaler.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/155866344211276_en-US.png)


## Troubleshooting {#section_dd4_trg_1fb .section}

-   If the cluster autoscaler cannot add nodes to a pod that requested more resources, you can perform the following checks:
    -   Make sure that the amount of resources provided by the ECS instances that you set for the autoscaling group is greater than the amount of resources requested by the pod.
    -   Make sure that you have granted the required permissions by following the preceding steps. You must grant the required permissions for each target cluster.
    -   Make sure that the target Kubernetes cluster are connected to the Internet. The cluster autoscaler calls an API action from Alibaba Cloud, therefore you must ensure that the cluster nodes can be accessed through the Internet.
-   If the cluster autoscaler cannot delete nodes from the autoscaling group, you can perform the following checks:

    -   Make sure that the resource request threshold of pods on all nodes is not greater than that of used to scale in the cluster.
    -   Make sure that no node run the pods that belong to the kube-system namespace.
    -   Make sure that no node runs the pod for which any constrained scheduling policies are set. This is because a constrained scheduling policy can limit a pod to a fixed node.
    -   Make sure that no pod contains a `PodDisruptionBudget` object that has reached the minimum value allowed. For more information, see How do Disruption Budgets work. For more information, see [How do Disruption Budgets work](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/#how-disruption-budgets-work).
    For more information, see [Cluster autoscaler](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md).


