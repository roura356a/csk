# Cluster auto scaling {#concept_bpt_qvm_z2b .concept}

Configure a cluster to auto scale according to the cluster load.

## Prerequisites {#section_grh_5dh_1fb .section}

You have created a Kubernetes cluster successfully. For how to create a Kubernetes cluster, see [EN-US\_TP\_16639.md\#](reseller.en-US/.md#).

## Background {#section_vlh_12h_1fb .section}

Cluster auto scaling is different from [EN-US\_TP\_16647.md\#](reseller.en-US/User Guide/Kubernetes cluster/集群管理/Scale out or in a cluster.md#) that is based on resource thresholds. After auto scaling is configured for a cluster, the cluster automatically scales out or scales in when the cluster load reaches the configured value.

## Procedure {#section_dd4_trg_1fb .section}

**Enable cluster auto scaling**

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Under Kubernetes, click **Clusters** in the left-side navigation pane.
3.  Select a cluster and click **More** \> **Auto Scaling** in the Action column.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/153667314111191_en-US.png)


**Authorization**

-   **Activate Auto Scaling service**

1.  Click the first link in the displayed dialog box.****

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/153667314111211_en-US.png)

2.  Click **Activate Auto Scaling service**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/153667314111263_en-US.png)

3.  Select the **I agree with Auto Scaling Service Agreement of Service** check box and click **Enable Now**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/153667314111264_en-US.png)

4.  After the service is activated, click **Console**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/153667314111265_en-US.png) 

5.  Click **Authorize**. Configure permissions for accessing cloud resources on the Cloud Resource Access Authorization page.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/153667314111266_en-US.png)

6.  Click **Confirm Authorization Policy**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/153667314111267_en-US.png)

    **Expected result**

    When the page automatically jumps to the elastic scaling console, the authorization succeeds. Closes the page and continues to configure **Authorize roles**.

-   **Authorize a role.**

1.  Click the second link in the displayed dialog box. ****

    **Note:** Use the primary account to log on to the console.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/153667314111261_en-US.png)

2.  Select the target authorization policy and click **View Permissions** in the Action column.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/153667314111193_en-US.png)

3.  Click **Modify Authorization Policy** in the upper-right corner of the page.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/153667314111195_en-US.png) 

4.  In the `Action` field of **Policy content**, enter the following:

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

    **Note:** Add a comma \(,\) to the last line in the `Action` field before entering the preceding content.

5.  Click **Modify Authorization**.

**Configure cluster auto scaling**

1.  On the Auto-scaling page, configure the following parameters:

    |Configuration|Description|
    |-------------|-----------|
    |Cluster|The target cluster name.|
    |Shrinkage Threshold|The ratio of the amount of resources requested by the cluster load to the amount of cluster resources. When the amount of resource requested by the cluster load is less than or equal to the configured shrinkage threshold, the cluster automatically shrinks. The default is 50%.|
    |Shrinkage Trigger Delay|When the configured shrinkage threshold is reached and the configured shrinkage trigger delay expires, the cluster starts to shrinks. Unit: minute The default is 10 minutes.|
    |Cooldown Time|After cluster expansion or shrinkage, the cooldown time starts to count. During the cooldown time, adding nodes to or removing nodes from the cluster does not trigger the cluster to expand or shrink again. The default is 10 minutes.|

2.  Select a resource type \(CPU or GPU\) to be scaled, click **Create** in the Action column.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/153667314211210_en-US.png)

    On the Scaling Group Config page, configure the following parameters to create a scaling group:

    |Configuration|Description |
    |-------------|------------|
    |Region|The region to which the created scaling group is deployed. This region must be consistent with the region of the cluster in which the scaling group resides. This region cannot be changed.|
    |Zone|The zone of the created scaling group.|
    |VPC|The network of the created scaling group, which must be consistent with the network of the cluster in which the scaling group resides.|

    Configure worker nodes

    |Configuration|Description|
    |-------------|-----------|
    |Instance Type|Types of instances in the scaling group.|
    |System Disk|The system disk of the scaling group.|
    |Attach Data Disk|Whether or not to mount a data disk when you create a scaling group. By default, no data disk is not mounted.|
    |Instance Quantity|The number of instances contained by the scaling group.**Note:** 

    -   Existing instances are not included.
    -   By default, the minimum number of instances is 0. When the number of instances exceeds 0, the cluster adds an instance to the scaling group and adds the instance into the Kubernetes cluster in which the scaling group resides by default.
|
    |Key Pair|The key pair used to log on to the scaled node. You can create a new key pair on the Elastic Compute Service \(ECS\) console.**Note:** Only key pair logon is supported currently.

|
    |RDS Whitelist|The Relational Database Service \(RDS\) instance that can be accessed by a scaled node.|

3.  Click **OK** to create a scaling group.

**Expected result**

-   A scaling group is displayed under CPU on the Auto-scaling page.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/153667314211273_en-US.png) 

-   1.  Click **Application** \> **Deployment** in the left-side navigation pane.
2.  Select the target cluster and the kube-system namespace, you can view the created component named cluster-autoscaler.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/19071/153667314211276_en-US.png)


