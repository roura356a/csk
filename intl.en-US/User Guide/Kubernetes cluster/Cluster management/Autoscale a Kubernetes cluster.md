# Autoscale a Kubernetes cluster {#concept_bpt_qvm_z2b .concept}

This topic describes how to autoscale a Kubernetes cluster in response to the cluster workload.

## Prerequisites {#section_grh_5dh_1fb .section}

A Kubernetes cluster is created. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).

## Background information {#section_vlh_12h_1fb .section}

Autoscaling a Kubernetes cluster is different from [Scale a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Scale a Kubernetes cluster.md#), which is based on resource thresholds. An autoscaling-enabled cluster is automatically scaled out or scaled in when the cluster workload reaches the autoscaling conditions that you set.

## Procedure {#section_dd4_trg_1fb .section}

**Enable cluster autoscaling** 

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Kubernetes, click **Clusters**.
3.  Find the target cluster. Then, in the **Action** column, choose **More** \> **Auto Scaling**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/155808606611191_en-US.png)


**Gran required permissions for the Auto Scaling service and the cluster**

-   **Activate the Auto Scaling service** 

1.  Click **ESS** in the displayed dialog box.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/155808606611211_en-US.png)

2.  Click **Activate Auto Scaling service**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/155808606611263_en-US.png)

3.  Read and confirm that you agree to the conditions by selecting the **I agree with Auto Scaling Service Agreement of Service** check box, and then click **Enable Now**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/155808606611264_en-US.png)

4.  Click **Console**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/155808606611265_en-US.png)

5.  Click **Authorize**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/155808606611266_en-US.png)

6.  Click **Confirm Authorization Policy** to grant ESS the permission to access your cloud resources.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/155808606611267_en-US.png)

    **Verify the result**

    If the page automatically redirects to the Auto Scaling console, the activation is successful.

-   **Add ESS authorization policies to the cluster** 

1.  Click the Worker RAM role \(`KubernetesWorkerRole[xxx]` \) in the following dialog box.

    **Note:** You need to use the primary account to log on to the console before perform this operation.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/155808606711261_en-US.png)

2.  Click **View Permissions** on the right of the target authorization policy.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/155808606711193_en-US.png)

3.  In the upper-right corner of the page, click **Modify Authorization Policy**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/155808606711195_en-US.png)

4.  In the `Action` field of the **Policy Document** area, add the following policies:

    ```
    
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

**Set cluster auto scaling parameters** 

1.  On the Automatic Scaling page, set the following parameters.

    |Configuration|Description|
    |-------------|-----------|
    |Cluster|Target cluster name.|
    |Scale-in Threshold|The ratio of the amount of resources requested by the cluster workload to the amount of cluster resources. When the amount of resources requested by the cluster workload is less than or equal to the threshold, the system automatically scales in the cluster. The default value is 50%.|
    |Defer Scale-in For|The number of minutes for which the system must wait to automatically scale in the cluster after the scale-in threshold is reached. The default value is 10 minutes.|
    |Cool-Down Time|The period \(in minutes\) during which the system does not automatically scale in or scale out a cluster after the number of cluster nodes increases or decreases. The default is 10 minutes.|

2.  Click **Create** on the right of the target type of resource \(which can be CPU or GPU\) that you want to autoscale.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/155808606711210_en-US.png)

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

**Verify the result**

-   You can directly verify that a scaling group under CPU is displayed on the Auto Scaling page.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/155808606711273_en-US.png)

-   To verify the created autoscaling component, follow these steps:

    1.  In the left-side navigation pane, choose **Application** \> **Deployment**.
    2.  Select the target cluster and the kube-system namespace to view the created component named cluster-autoscaler.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/155808606711276_en-US.png)


