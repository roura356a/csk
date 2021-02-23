# Auto scaling of nodes

ACK provides the auto scaling component \(cluster-autoscaler\) for nodes to automatically scale in and out. Regular instances, GPU-accelerated instances, and preemptible instances can be automatically added to or removed from an ACK cluster based on your requirements. This feature provides multiple scaling modes, supports various instance types and multi-zone instances, and meets your scaling requirements in different scenarios.

## How it works

The auto scaling mechanism of Kubernetes is different from the traditional scaling model, which is based on resource usage. This is also the most difficult part when developers migrate workloads from traditional data centers or other orchestration systems \(such as Swarm\) to Kubernetes.

The traditional scaling model is designed based on resource usage. Assume that a cluster has three nodes. When the CPU usage or memory usage exceeds the scaling threshold, new nodes are added to the cluster. However, you need to consider the following issues when you use the traditional scaling model:



How does the auto scaling mechanism of Kubernetes fix this issue? The auto scaling mechanism of Kubernetes fixes this issue by decoupling the scheduling of pods from the scaling of computing resources in a cluster.

In simple terms, pod replicas are scaled based on resource usage. This is how pods are scaled. However, when the computing resources are insufficient for scheduling a pod, a scale-out event is triggered. After new nodes are added to the cluster, pending pods are automatically scheduled to these nodes. This way, the load on nodes is reduced. The following section describes the details of the auto scaling mechanism of Kubernetes:

## Considerations

-   For each account, the default CPU quota of pay-as-you-go instances in each region is 50 vCPUs. You can create up to 48 custom route entries in each route table in a virtual private cloud \(VPC\). To request a quota increase, submit a ticket.
-   The stock availability of a specific ECS instance type greatly fluctuates. We recommend that you specify multiple instance types for a scaling group. This improves the success rate of auto scaling.
-   In swift mode, when a node is shut down and reclaimed, it stops running and remains in the NotReady state. When a scale-out event is triggered, the state of the node is changed to Ready.
-   When a node is shut down and reclaimed in swift mode, you are charged for only the storage costs of the disk. This rule does not apply to nodes that use local disks, such as the instance type of ecs.d1ne.2xlarge, where the computing costs are also charged. If the stock of the node resources is sufficient, nodes can be launched in a short period of time.
-   If elastic IP addresses \(EIPs\) are bound to pods, we recommend that you do not delete the scaling group or the ECS nodes that are added from the scaling group in the ECS console. Otherwise, these EIPs cannot be automatically released.

## Step 1: Go to the Configure Auto Scaling page

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, perform the following steps to go to the **Configure Auto Scaling** page.

    You can go to the Configure Auto Scaling page in the following ways:

    -   Method 1: Find the cluster that you want to manage and choose **More** \> **Auto Scaling** in the **Actions** column.
    -   Method 2:
        1.  Find the cluster that you want to manage and click **Details** in the Actions column.
        2.  In the left-side navigation pane, choose **Nodes** \> **Node Pools**.
        3.  In the upper-right corner of the Node Pools page, click **Configure Auto Scaling**.

## Step 2: Authorization

You need to perform authorization in the following scenarios:



If the current account has limited permissions on nodes in the cluster, you need to assign the **AliyunCSManagedAutoScalerRole** Resource Access Management \(RAM\) role to your account.

**Note:** You only need to perform the authorization once for each Alibaba Cloud account.

1.  Activate Auto Scaling \(ESS\).

    1.  In the dialog box that appears, click the first hyperlink to go to the **ESS** console.

    2.  Click **Activate Auto Scaling** to go to the Enable Service page.

    3.  Select the **I agree with Auto Scaling Agreement of Service** check box and click **Enable Now**.

    4.  On the Activated page, click **Console** to go to the ESS console.

    5.  Click **Go to Authorize** to go to the Cloud Resource Access Authorization page, and then authorize ESS to access other cloud resources.

    6.  Click **Agree to Authorization**.

2.  Assign the RAM role.

    1.  Click the second hyperlink in the dialog box.

        **Note:** You must log on to the RAM console by using an Alibaba Cloud account.

    2.  On the **Cloud Resource Access Authorization** page, click **Agree to Authorization Policy**.


1.  Activate ESS.

    1.  In the dialog box that appears, click the first hyperlink to go to the **ESS** console.

        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7285268951/p11211.png)

    2.  Click **Activate Auto Scaling** to go to the Enable Service page.

    3.  Select the **I agree with Auto Scaling Agreement of Service** check box and click **Enable Now**.

    4.  On the Activated page, click **Console** to go to the ESS console.

    5.  Click **Go to Authorize** to go to the Cloud Resource Access Authorization page, and then authorize ESS to access other cloud resources.

    6.  Click **Agree to Authorization**.

    If the authorization is successful, you are redirected to the ESS console. Close the page and [assign the RAM role](#step_dkz_59w_xec).

2.  Assign the RAM role.

    1.  Click the second hyperlink in the dialog box to go to the **RAM** console.

        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8285268951/p11261.png)

        **Note:** You must log on to the RAM console by using an Alibaba Cloud account.

    2.  Click the **Permissions** tab and click the name of the policy that you want to manage. The details page of the policy appears.

        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8285268951/p11193.png)

    3.  Click **Modify Policy Document**. The Modify Policy Document panel appears on the right side of the page.

        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8285268951/p11195.png)

    4.  In the **Policy Document** section, add the following policy content to the `Action` field and click **OK**.

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

        **Note:** Add a comma \(,\) to the end of the bottom line in the `Action` field before you add the policy content.


If an auto-scaling node pool in the cluster is required to be associated with an EIP, perform the following steps to grant permissions.

1.  Activate ESS.

    1.  In the dialog box that appears, click the first hyperlink to go to the **ESS** console.

        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7285268951/p11211.png)

    2.  Click **Activate Auto Scaling** to go to the Enable Service page.

    3.  Select the **I agree with Auto Scaling Agreement of Service** check box and click **Enable Now**.

    4.  On the Activated page, click **Console** to go to the ESS console.

    5.  Click **Go to Authorize** to go to the Cloud Resource Access Authorization page, and then authorize ESS to access other cloud resources.

    6.  Click **Agree to Authorization**.

    If the authorization is successful, you are redirected to the ESS console. Close the page and [assign the RAM role](#step_dkz_59w_xec).

2.  Assign the RAM role.

    1.  Click the second hyperlink in the dialog box to go to the **RAM** console.

        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8285268951/p11261.png)

        **Note:** You must log on to the RAM console by using an Alibaba Cloud account.

    2.  Click the **Permissions** tab and click the name of the policy that you want to manage. The details page of the policy appears.

        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8285268951/p11193.png)

    3.  Click **Modify Policy Document**. The Modify Policy Document panel appears on the right side of the page.

        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8285268951/p11195.png)

    4.  In the **Policy Document** section, add the following policy content to the `Action` field:

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

        **Note:** Add a comma \(,\) to the end of the bottom line in the `Action` field before you add the policy content.

    5.  On the **RAM Roles** page, click the name of the RAM role that you want to manage. On the details page of the RAM role, click the **Trust Policy Management** tab and click **Edit Trust Policy**. In the Edit Trust Policy panel, add oos.aliyuncs.com to the Service field, as shown in the following figure. Click **OK**.

        ![oos](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8285268951/p103678.png)


## Step 3: Configure auto scaling

1.  On the Configure Auto Scaling page, set the following parameters and click **Submit**.

    |Parameter|Description|
    |---------|-----------|
    |Cluster|The name of the cluster for which you want to enable auto scaling.|
    |Scale-in Threshold|In a scaling group managed by cluster-autoscaler, the system automatically calculates the ratio of the requested resources to the resource capacity of a node. If the ratio is lower than the threshold, nodes are removed from the ACK cluster. **Note:** Scale-out events are automatically triggered based on pod scheduling. Therefore, you only need to configure scale-in rules. |
    |Defer Scale-in For|The amount of time that the cluster must wait before the cluster scales in. Unit: minutes. The default value is 10 minutes.|
    |Cooldown|The cooldown period after a scale-in event is triggered. No scale-in event is triggered again during this period of time. Unit: minutes. The default value is 10 minutes.|

2.  Select an instance type based on your workload requirements, such as a regular instance, a GPU-accelerated instance, or a preemptible instance. Then, click **Create**.

3.  In the Auto Scaling Group Configuration dialog box, set the following parameters to create a scaling group.

    |Parameter|Description|
    |---------|-----------|
    |Region|The region where the scaling group is deployed. The scaling group and the cluster must be deployed in the same region. You cannot change the region after the scaling group is created.|
    |VPC|The scaling group and the cluster must be deployed in the same VPC.|
    |VSwitch|The vSwitches of the scaling group. The zone and CIDR block of each vSwitch belong to those of the scaling group.|

4.  Configure worker nodes.

    |Parameter|Description|
    |---------|-----------|
    |Node Type|The types of nodes in the scaling group. The node types must be the same as those that are selected when the cluster was created.|
    |Instance Type|The instance types in the scaling group.|
    |Selected Types|The instance types that you have selected. You can select up to 10 instance types.|
    |System Disk|The system disk of the scaling group.|
    |Mount Data Disk|Specifies whether to mount data disks to the scaling group. By default, no data disk is mounted.|
    |Quantity|The number of instances in the scaling group. **Note:**

    -   Existing instances in the cluster are excluded.
    -   By default, the minimum number of instances is 0. If you specify one or more instances, the system will add instances to the scaling group. When a scale-out event is triggered, instances in the scaling group are added to the cluster to which the scaling group is bound. |
    |Key Pair|The key pair that is used to log on to the nodes in the scaling group. You can create key pairs in the ECS console. **Note:** You can log on to the nodes only by using key pairs. |
    |Scaling Mode|You can select **Standard** or **Swift**.|
    |RDS Whitelist|The Relational Database Service \(RDS\) instances that can be accessed by nodes that are added by scale-out events.|
    |Node Label|Node labels are automatically added to nodes that are added to the cluster by scale-out events.|
    |Taints|After you add taints to a node, ACK does not schedule pods to the node.|

5.  Click **OK** to create the scaling group.


## Check the results

1.  On the Auto Scaling page, you can find the newly created scaling group below Regular Instance.

    ![Auto Scaling](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7922234061/p11273.png)

2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

4.  On the **Deployments** tab, select the kube-system namespace. You can find the cluster-autoscaler component. This indicates that the scaling group is created.


## FAQ

-   Why does the auto scaling component fail to add nodes after a scale-out event is triggered?

    Check for the following issues:

    -   Whether instance types that are configured for the scaling group meet the requested resources of pods. By default, system components are installed for each node. Therefore, the requested resources of pods on a node must be less than the resource capacity of the instance type of the node.
    -   Whether you have performed authorization steps as described. You must perform the authorization for each cluster that is involved in the scale-out event.
    -   Whether the cluster can access the Internet. Nodes in a scaling group require Internet access. This is because the auto scaling component needs to call Alibaba Cloud APIs over the Internet.
-   Why does the auto scaling component fail to remove nodes after a scale-in event is triggered?

    Check for the following issues:

    -   Whether the ratio of the requested resources to the resource capacity of each node is higher than the scale-in threshold.
    -   Whether the pods in the kube-system namespace are running on nodes.
    -   Whether the pods are configured with a forcible scheduling policy, which forbids these pods to be scheduled to other nodes.
    -   Whether the pods on the nodes are configured with a [PodDisruptionBudget](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/#how-disruption-budgets-work) and the number of pods has reached the specified minimum value.
    For more frequently asked questions about the [auto scaling component](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md), visit the open source community.

-   How does the system select from multiple scaling groups for a scale-out event?

    When pods cannot be scheduled to nodes, the auto scaling component will simulate the scheduling of pods based on the configuration of the scaling group. The configuration includes the labels, taints, and instance specifications. If a scaling group that meets the requirements, this scaling group is selected for the scale-out event. If more than one scaling group meet the requirements, the system chooses the scaling group that has the fewest remaining resources after the simulation.


**Related topics**  


[Access the Kubernetes API server over the Internet](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Access the Kubernetes API server over the Internet.md)

[HPA](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/HPA.md)

[Implement horizontal auto scaling based on Alibaba Cloud metrics](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Implement horizontal auto scaling based on Alibaba Cloud metrics.md)

[CronHPA](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/CronHPA.md)

[Vertical pod autoscaling](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Vertical pod autoscaling.md)

