# Auto scaling of nodes

Alibaba Cloud Container Service for Kubernetes \(ACK\) provides the auto scaling component to enable automatic node scaling. General purpose instances, GPU-accelerated instances, and preemptible instances can be automatically added to or removed from an ACK cluster based on your requirements. This feature is applicable to instances deployed across multiple zones and diverse instance types, and also supports different scaling modes.

## How it works

The auto scaling feature automatically scales nodes based on how resources are allocated in an ACK cluster. The CPU and memory resources of a node are allocated based on requests from pods. When pods on a node are overwhelmed by resource requests, the pods become pending. The auto scaling component then automatically calculates the number of nodes required to be added based on the predefined resource specifications and constraints of the scaling group. If the scaling requirements are met, the nodes in the scaling group are added to the ACK cluster. If a node belongs to a scaling group and the requested resources of the pods on the node are less than the threshold, the auto scaling component automatically removes the node from the cluster. Therefore, you must set an appropriate threshold of requested resources to ensure efficient auto scaling.

## Considerations

-   For each account, the default CPU quota of pay-as-you-go instances in each region is 50 vCPUs. You can create a maximum of 48 custom route entries in each route table in a virtual private cloud \(VPC\). To request a quota increase, submit a ticket.
-   If you select ECS instances of the same specification for auto scaling, the availability of ECS instances fluctuates. We recommend that you configure multiple instance types of the same specification in a scaling group. This improves the success rate of auto scaling.
-   In Swift mode, when a node is shut down for recycling, the node enters the NotReady state. When a scale-out event is triggered, the node enters the Ready state.
-   In Swift mode, you are charged only for disk space when a node is shut down for recycling. This rule does not apply to nodes that use local disks, such as ecs.d1ne.2xlarge.
-   If you have bound elastic IP addresses \(EIPs\) to the pods, we recommend that you do not delete the auto scaling group or the ECS instances scaled out by the scaling group from the ECS console. Otherwise, the EIPs cannot be automatically released.

## Step 1: Go to the Configure Auto Scaling page

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  Use one of the following methods to go to the **Configure Auto Scaling** page:

    -   Find the cluster that you want to scale, and choose **More** \> **Auto Scaling** in the Actions column.
    -   1.  Find the cluster that you want to scale, and click **Details** in the Actions column.
2.  In the left-side navigation pane, click **Node Pools**.
3.  In the upper-right corner, click **Configure Auto Scaling**.

## Step 2: Authorize the current user to access Auto Scaling

1.  Perform the following steps to activate Auto Scaling:

    1.  In the dialog box that appears, click Auto Scaling \(ESS\) to go to the **Auto Scaling** console.

        ![Activate Auto Scaling](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7285268951/p11211.png)

    2.  Click **Activate Auto Scaling** to go to the Enable Service page.

        ![Enable Service](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8285268951/p11263.png)

    3.  Select **I agree with Auto Scaling Service Agreement of Service** and click **Enable Now**.

    4.  On the Activated page, click **Console** to return to the Auto Scaling console.

    5.  Click **Authorize** to go to the Cloud Resource Access Authorization page, and then authorize Auto Scaling to access other cloud resources.

    6.  Click **Confirm Authorization Policy**.


Expected results

The authorization succeeds. You are redirected to the Auto Scaling console. Go to the page in Step 1 and continue to [Step 3: Authorize RAM roles](#section_07o_yhn_131).

## Step 3: Authorize RAM roles

1.  Click the third hyperlink in the dialog box to go to the **RAM** console.

    **Note:** You must log on to the RAM console by using an Alibaba Cloud account.

    ![RAM Roles](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8285268951/p11261.png)

2.  Click the **Permissions** tab. Click the name of the policy that you want to modify. The details page of the policy appears.

    ![Permissions](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8285268951/p11193.png)

3.  Click **Modify Policy Document**. The Modify Policy Document page appears on the right.

4.  Add the following content to the `Action` field in the **Policy Document** section:

    ```
    
    "ess:Describe*", 
    "ess:CreateScalingRule", 
    "ess:ModifyScalingGroup", 
    "ess:RemoveInstances", 
    "ess:ExecuteScalingRule", 
    "ess:ModifyScalingRule", 
    "ess:DeleteScalingRule", 
    "ecs:DescribeInstanceTypes",
    "ess:DetachInstances",
    "vpc:DescribeVSwitches"
    ```

    **Note:** Add a comma \(,\) to the end of the bottom line in the `Action` field before you add the content.

    To bind EIPs to the scaling group, add the following policies:

    ```
    "ecs:AllocateEipAddress",
    "ecs:AssociateEipAddress",
    "ecs:DescribeEipAddresses",
    "ecs:DescribeInstanceTypes",
    "ecs:DescribeInvocationResults",
    "ecs:DescribeInvocations",
    "ecs:ReleaseEipAddress",
    "ecs:RunCommand",
    "ecs:UnassociateEipAddress",
    "ess:CompleteLifecycleAction",
    "ess:CreateScalingRule",
    "ess:DeleteScalingRule",
    "ess:Describe*",
    "ess:DetachInstances",
    "ess:ExecuteScalingRule",
    "ess:ModifyScalingGroup",
    "ess:ModifyScalingRule",
    "ess:RemoveInstances",
    "vpc:AllocateEipAddress",
    "vpc:AssociateEipAddress",
    "vpc:DescribeEipAddresses",
    "vpc:DescribeVSwitches",
    "vpc:ReleaseEipAddress",
    "vpc:UnassociateEipAddress",
    "vpc:TagResources"
    ```

    On the **RAM Roles** page, click **Trust Policy Management**. Then, click **Edit Trust Policy**. On the page that appears, add oos.aliyuncs.com to the field, as shown in the following figure.

    ![oos](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8285268951/p103678.png)

5.  Click **OK**.


## Step 4: Configure auto scaling

1.  On the Auto Scaling page, set the following parameters and click **Submit**.

    |Parameter|Description|
    |---------|-----------|
    |Cluster|The name of the cluster.|
    |Scale-in Threshold|For a scaling group managed by cluster-autoscaler, set this parameter to a value that is calculated by dividing the volume of requested resources on a node by the resource capacity of the node. If the actual value is lower than the threshold, the node is removed from the cluster. **Note:** In auto scaling, scale-out is automatically triggered based on the node scheduling of the cluster. Therefore, you are required to set scale-in parameters only. |
    |Defer Scale-in For|The time to wait after the scale-in threshold is met and before the scale-in event starts. Unit: minutes. Default value: 10.|
    |Cooldown|The cooldown period after a node is removed. No scale-in events are triggered during this period. Unit: minutes. Default value: 10.|

2.  Select an instance type based on your workload requirements, such as a general purpose instance, an GPU-accelerated ECS instance, or a preemptible instance. Then, click **Create**.

    ![Resource types](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8285268951/p11210.png)

3.  On the Auto Scaling Group Configuration page, set the following parameters to create a scaling group.

    |Parameter|Description|
    |---------|-----------|
    |Region|The region where the scaling group is deployed. The scaling group and the cluster must be deployed in the same region. You cannot change the region after the scaling group is created.|
    |VPC|The scaling group and the cluster must be deployed in the same VPC.|
    |VSwitch|Specifies the zone of the scaling group and the pod CIDR blocks that are available to the scaling group.|

4.  Configure worker nodes.

    |Parameter|Description|
    |---------|-----------|
    |Node Type|The types of nodes in the scaling group. The node types must be the same as those in the cluster.|
    |Instance Type|The instance types in the scaling group.|
    |Selected Types|The instance types that you have selected. You can select a maximum of 10 instance types.|
    |System Disk|The system disk of the scaling group.|
    |Mount Data Disk|Specifies whether to mount data disks to the scaling group. By default, no data disk is mounted.|
    |Instances|The number of instances in the scaling group. **Note:**

    -   Existing instances in the cluster are not included.
    -   By default, the minimum number of instances is zero. When the number of instances exceeds zero, the system adds instances to the scaling group. When a scale-out event is triggered, instances in the scaling group are added to the cluster where the scaling group resides. |
    |Key Pair|The key pair used to log on to the nodes after the nodes are added to the ACK cluster. You can create key pairs in the ECS console. **Note:** Only key pair logon is supported. |
    |Scaling Mode|You can select **Standard** or **Swift**.|
    |RDS whitelist|The Relational Database Service \(RDS\) instances that can be accessed by the nodes in the scaling group after a scale-out event is triggered.|
    |Node Label|Node labels are automatically attached to the nodes that are added to the ACK cluster after a scale-out event is triggered.|
    |Taints|After you add taints to a node, ACK does not schedule pods to the node.|

5.  Click **OK** to create the scaling group.


## Expected results

1.  On the Auto Scaling page, you can find the newly created scaling group in the general purpose instance section.

    ![Auto Scaling](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7922234061/p11273.png)

2.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane, click **Workload**.

4.  On the **Deployments** tab, select the scaled cluster and select the kube-system namespace. You can find the cluster-autoscaler component. This indicates that the scaling group is created.


## FAQ

-   Why does the auto scaling component fail to add nodes after a scale-out event is triggered?

    Check whether the following scenarios exist:

    -   The instance types in the scaling group cannot meet the resource requests of pods. By default, system components are installed for each node. Therefore, the requested pod resources must be less than the resource capacity of the instance type.
    -   You have not authorized the RAM roles to manage the cluster. Authorization must be performed on each cluster involved in the scale-out event.
    -   The cluster cannot connect to the Internet. The auto scaling component must call Alibaba Cloud APIs, which requires that the nodes have Internet access.
-   Why does the auto scaling component fail to remove nodes after a scale-in event is triggered?

    Check whether the following scenarios exist:

    -   The resource request threshold of each pod is higher than the configured scale-in threshold.
    -   Pods in the kube-system namespace are running on the node.
    -   The pod to be removed contains a forced scheduling policy. As a result, other nodes cannot run this pod.
    -   The pods on the node have [PodDisruptionBudget](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/#how-disruption-budgets-work) and reach the minimum value of PodDisruptionBudget.
    For more information about the auto scaling feature, see descriptions in the [open source community](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md).

-   How does the system select from multiple scaling groups for a scale-out event?

    When a cluster fails to schedule pods due to excessive resource requests, the auto scaling feature simulates pod scheduling and selects a scaling group based on group labels, taints, and instance specifications. If a scaling group meets the conditions for pod scheduling, this group is selected for auto scaling. If multiple scaling groups meet the conditions, the cluster selects a scaling group that has the fewest remaining resources after simulated pod scheduling.


**Related topics**  


[Access the Kubernetes API server over the Internet](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Access the Kubernetes API server over the Internet.md)

[HPA](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/HPA.md)

[Implement horizontal auto scaling based on Alibaba Cloud metrics](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Implement horizontal auto scaling based on Alibaba Cloud metrics.md)

[Implement cron HPA](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Implement cron HPA.md)

[Vertical pod autoscaling](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Vertical pod autoscaling.md)

