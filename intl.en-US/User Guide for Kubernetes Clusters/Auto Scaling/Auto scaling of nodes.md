---
keyword: [auto scaling, nodes]
---

# Auto scaling of nodes

Container Service for Kubernetes \(ACK\) provides the auto scaling component \(cluster-autoscaler\) to automatically scale nodes. Regular instances, GPU-accelerated instances, and preemptible instances can be automatically added to or removed from an ACK cluster to meet your business requirements. This component supports multiple scaling modes, various instance types, and instances that are deployed across zones. This component is applicable to diverse scenarios.

## How an Ingress controller works

The auto scaling model of Kubernetes is different from the traditional scaling model that is based on the resource usage threshold. Developers must understand the differences between the two scaling models before they migrate workloads from traditional data centers or other orchestration systems such as Swarm to Kubernetes.

The traditional scaling model is based on resource usage. For example, if a cluster contains three nodes and the CPU utilization or memory usage of the nodes exceeds the scaling threshold, new nodes are added to the cluster. However, you must consider the following issues when you use the traditional scaling model:



How does the auto scaling model of Kubernetes fix these issues? Kubernetes provides a two-layer scaling model that decouples pod scheduling from resource scaling.

Pods are scaled based on resource usage. When pods enter the Pending state due to insufficient resources, a scale-out activity is triggered. After new nodes are added to the cluster, the pending pods are automatically scheduled to the newly added nodes. This way, the loads of the application are balanced. The following section describes the auto scaling model of Kubernetes in detail:

## Precautions

-   For each account, the default CPU quota for pay-as-you-go instances in each region is 50 vCPUs. You can add at most 48 custom route entries to each route table of a virtual private cloud \(VPC\). To increase the quota, submit a ticket.
-   The stock of ECS instances may be insufficient for auto scaling if you specify only one ECS instance type for a scaling group. We recommend that you specify multiple ECS instance types with the same specification for a scaling group. This increases the success rate of auto scaling.
-   In swift mode, when a node is shut down and reclaimed, the node stops running and enters the NotReady state. When a scale-out activity is triggered, the state of the node changes to Ready.
-   If a node is shut down and reclaimed in swift mode, you are charged only for the disks. This rule does not apply to nodes that use local disks, such as the instance type of ecs.d1ne.2xlarge, for which you are also charged a computing fee. If the stock of nodes is sufficient, nodes can be launched within a short period of time.
-   If elastic IP addresses \(EIPs\) are bound to pods, we recommend that you do not delete the scaling group or the ECS nodes that are added from the scaling group in the ECS console. Otherwise, these EIPs cannot be automatically released.

## Step 1: Configure auto scaling

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, follow the instructions to navigate to the **Configure Auto Scaling** page.

    You can navigate to the Configure Auto Scaling page in the following ways:

    -   Method 1: Find the cluster that you want to manage and choose **More** \> **Auto Scaling** in the **Actions** column.
    -   Method 2:
        1.  Find the cluster that you want to manage and click **Details** in the Actions column.
        2.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.
        3.  In the upper-right corner of the Node Pools page, click **Configure Auto Scaling**.

## Step 2: Perform authorization

You must perform authorization in the following scenarios:



If ACK has limited permissions on nodes in the cluster, assign the **AliyunCSManagedAutoScalerRole** Resource Access Management \(RAM\) role to ACK.

**Note:** You need to perform the authorization only once for each Alibaba Cloud account.

1.  Activate Auto Scaling \(ESS\).

    1.  In the dialog box that appears, click the first hyperlink to log on to the **ESS** console.

    2.  Click **Activate Auto Scaling** to go to the Enable Service page.

    3.  Select the **I agree with Auto Scaling Agreement of Service** check box and click **Enable Now**.

    4.  On the Activated page, click **Console** to log on to the ESS console.

    5.  Click **Go to Authorize** to go to the Cloud Resource Access Authorization page. Then, authorize ESS to access other cloud resources.

    6.  Click **Confirm Authorization Policy**.

2.  Assign the RAM role.

    1.  Click the second hyperlink in the dialog box.

        **Note:** This step requires you to log on the console with an Alibaba Cloud account.

    2.  On the **Cloud Resource Access Authorization** page, click **Confirm Authorization Policy**.


1.  Activate ESS.

    1.  In the dialog box that appears, click the first hyperlink to log on to the **ESS** console.

        ![](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/7285268951/p11211.png)

    2.  Click **Activate Auto Scaling** to go to the Enable Service page.

    3.  Select the **I agree with Auto Scaling Agreement of Service** check box and click **Enable Now**.

    4.  On the Activated page, click **Console** to log on to the ESS console.

    5.  Click **Go to Authorize** to go to the Cloud Resource Access Authorization page. Then, authorize ESS to access other cloud resources.

    6.  Click **Confirm Authorization Policy**.

    If the authorization is successful, you are redirected to the ESS console. Close the page and [modify the permissions of the worker RAM role](#step_dkz_59w_xec).

2.  Modify the permissions of the worker RAM role.

    1.  Click the second hyperlink in the dialog box to go to the **RAM Roles** page.

        ![](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/8285268951/p11261.png)

        **Note:** You must log on to the console with an Alibaba Cloud account.

    2.  On the **Permissions** tab, click the name of the policy assigned to the RAM role. The details page of the policy appears.

        ![](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/8285268951/p11193.png)

    3.  Click **Modify Policy Document**. The Modify Policy Document panel appears on the right side of the page.

        ![](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/8285268951/p11195.png)

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

        **Note:** Before you add the policy content, add a comma \(,\) to the end of the bottom line in the `Action` field.


If you want to associate an auto-scaling group with an EIP, perform the following steps to grant permissions:

1.  Activate ESS.

    1.  In the dialog box that appears, click the first hyperlink to log on to the **ESS** console.

        ![](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/7285268951/p11211.png)

    2.  Click **Activate Auto Scaling** to go to the Enable Service page.

    3.  Select the **I agree with Auto Scaling Agreement of Service** check box and click **Enable Now**.

    4.  On the Activated page, click **Console** to log on to the ESS console.

    5.  Click **Go to Authorize** to go to the Cloud Resource Access Authorization page. Then, authorize ESS to access other cloud resources.

    6.  Click **Confirm Authorization Policy**.

    If the authorization is successful, you are redirected to the ESS console. Close the page and [modify the permissions of the worker RAM role](#step_dkz_59w_xec).

2.  Modify the permissions of the worker RAM role.

    1.  Click the second hyperlink in the dialog box to go to the **RAM Roles** page.

        ![](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/8285268951/p11261.png)

        **Note:** You must log on to the console with an Alibaba Cloud account.

    2.  On the **Permissions** tab, click the name of the policy assigned to the RAM role. The details page of the policy appears.

        ![](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/8285268951/p11193.png)

    3.  Click **Modify Policy Document**. The Modify Policy Document panel appears on the right side of the page.

        ![](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/8285268951/p11195.png)

    4.  In the **Policy Document** section, add the following policy content to the `Action` field and click **OK**.

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

        **Note:** Before you add the policy content, add a comma \(,\) to the end of the bottom line in the `Action` field.

    5.  On the **RAM Roles** page, click the name of the worker RAM role. On the details page of the RAM role, click the **Trust Policy Management** tab and click **Edit Trust Policy**. In the Edit Trust Policy panel, add oos.aliyuncs.com to the Service field, as shown in the following figure. Then, click **OK**.

        ![oos](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/8285268951/p103678.png)


## Step 3: Configure auto scaling

1.  On the Configure Auto Scaling page, set the following parameters and click **Submit**.

    |Parameter|Description|
    |---------|-----------|
    |Cluster|The name of the cluster for which you want to enable auto scaling.|
    |Scale-in Threshold|For a scaling group that is managed by cluster-autoscaler, set the value to the ratio of the requested resources per node to the total resources per node. If the actual value is lower than the threshold, the node is removed from the cluster. **Note:** In auto scaling, a scale-out activity is automatically triggered based on node scheduling. Therefore, you need to set only scale-in parameters. |
    |GPU Scale-in Threshold|The scale-in threshold for GPU-accelerated nodes. If the actual value is lower than the threshold, one or more GPU-accelerated nodes are removed from the Kubernetes cluster.|
    |Defer Scale-in For|The time to wait after the scale-in threshold is met and before the scale-in activity starts. Unit: minutes. The default value is 10 minutes.|
    |Cooldown|Newly added nodes cannot be removed in scale-in activities during the cool down period.|
    |Scan Interval|You can set this parameter to configure the interval at which the cluster is evaluated for scaling. Valid values: 15s, 30s, 60s. Default value: 30s.|

2.  Select an instance type. Supported instance types are regular instances, GPU-accelerated instances, and preemptible instances. Then, click **Create**.

3.  In the Auto Scaling Group Configuration dialog box, set the following parameters to create a scaling group.

    |Parameter|Description|
    |---------|-----------|
    |Region|The region where you want to deploy the scaling group. The scaling group and the Kubernetes cluster must be deployed in the same region. You cannot change the region after the scaling group is created.|
    |VPC|The scaling group and the Kubernetes cluster must be deployed in the same VPC.|
    |vSwitch|The vSwitches of the scaling group. You can specify vSwitches of different zones. The vSwitches allocate pod CIDR blocks to the scaling group.|

4.  Configure worker nodes.

    |Parameter|Description|
    |---------|-----------|
    |Node Type|The types of nodes in the scaling group. The node types must be the same as those selected when the cluster is created.|
    |Instance Type|The instance types in the scaling group.|
    |Selected Types|The instance types that you have selected. You can select at most 10 instance types.|
    |System Disk|The system disk of the scaling group.|
    |Mount Data Disk|Specify whether to mount data disks to the scaling group. By default, no data disk is mounted.|
    |Instances|The number of instances contained in the scaling group. **Note:**

    -   Existing instances in the cluster are excluded.
    -   By default, the minimum number of instances is 0. If you specify one or more instances, the system adds the instances to the scaling group. When a scale-out activity is triggered, the instances in the scaling group are added to the cluster to which the scaling group is bound. |
    |Key Pair|The key pair that is used to log on to the nodes in the scaling group. You can create key pairs in the ECS console. **Note:** You can log on to the nodes only by using key pairs. |
    |Scaling Mode|You can select **Standard** or **Swift**.|
    |RDS Whitelist|The ApsaraDB RDS instances that can be accessed by the nodes in the scaling group after a scaling activity is triggered.|
    |Node Label|Node labels are automatically added to nodes that are added to the cluster by scale-out activities.|
    |Taints|After you add taints to a node, Container Service no longer schedules pods to the node.|

5.  Click **Confirm Order** to create the scaling group.


## Check the results

1.  On the Auto Scaling page, you can find the newly created scaling group below Regular Instance.

    ![Auto Scaling](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/7922234061/p11273.png)

2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

4.  On the **Deployments** page, select the kube-system namespace. You can find the cluster-autoscaler component. This indicates that the scaling group is created.


## FAQ

-   Why does the auto scaling component fail to add nodes after a scale-out activity is triggered?

    Check whether the following situations exist:

    -   The instance types in the scaling group cannot fulfill the resource request from pods. By default, system components are installed for each node. Therefore, the requested pod resources must be less than the resource capacity of the instance type.
    -   The RAM role does not have the permissions to manage the Kubernetes cluster. You must configure RAM roles for each Kubernetes cluster that is involved in the scale-out activity.
-   Why does the auto scaling component fail to remove nodes after a scale-in activity is triggered?

    Check whether the following situations exist:

    -   The requested resource threshold of each pod is higher than the configured scale-in threshold.
    -   Pods that belong to the kube-system namespace are running on the node.
    -   A scheduling policy forces the pods to run on the current node. Therefore, the pods cannot be scheduled to other nodes.
    -   [PodDisruptionBudget](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/#how-disruption-budgets-work) is set for the pods on the node and the minimum value of PodDisruptionBudget is reached.
    For more information about FAQ, see [open source component](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md).

-   How does the system choose a scaling group for a scaling activity?

    When pods cannot be scheduled to nodes, the auto scaling component simulates the scheduling of the pods based on the configurations of scaling groups. The configurations include labels, taints, and instance specifications. If a scaling group meets the requirements, this scaling group is selected for the scale-out activity. If more than one scaling group meet the requirements, the system selects the scaling group that has the fewest idle resources after simulation.


**Related topics**  


[Control public access to the API server of a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Cluster access control/Control public access to the API server of a cluster.md)

[HPA](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/HPA.md)

[Implement horizontal auto scaling based on Alibaba Cloud metrics](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Implement horizontal auto scaling based on Alibaba Cloud metrics.md)

[CronHPA](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/CronHPA.md)

[Vertical pod autoscaling](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Vertical pod autoscaling.md)

