---
keyword: [hybrid cluster, auto scaling]
---

# Configure auto scaling

This topic describes how to enable auto scaling for a hybrid cluster.

## Prerequisites

When you configure auto scaling, the cluster-autoscaler component is automatically deployed as a Deployment in the hybrid cluster. Cloud computing nodes that are added to a cluster during a scale-out event may later be removed in a scale-in event. If you install system components that run as Deployments on these nodes, the system components may not be able to provide stable services. Therefore, you must make sure that these components are not scheduled to automatically added cloud computing nodes or on-premises nodes. The following conditions must be met:

-   A node pool is created and scaled out. For more information, see [t2057212.dita\#task\_2057212](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Hybrid clusters/Create and scale out a node pool.md).
-   The `alibabacloud.com/cloud-worker-nodes=true` label is added to nodes in the node pool. For more information, see [Manage node labels](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node/Manage node labels.md).

    This ensures that the cluster-autoscaler component is automatically scheduled to a node that has the specified label.


## Step 1: Configure auto scaling

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, perform the following steps to go to the **Configure Auto Scaling** page.

    You can go to the Configure Auto Scaling page in the following ways:

    -   Method 1: Find the cluster that you want to manage and choose **More** \> **Auto Scaling** in the **Actions** column.
    -   Method 2:
        1.  Find the cluster that you want to manage and click **Details** in the **Actions** column.
        2.  In the left-side navigation pane, choose **Nodes** \> **Node Pool**.
        3.  In the upper-right corner of the Node Pools page, click **Configure Auto Scaling**.
4.  On the Configure Auto Scaling page, set the parameters and then click submit.

    ![Auto scaling](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5101766161/p248329.png)

5.  Configure the cluster-autoscaler component.

    After you configure auto scaling, a Deployment of the cluster-autoscaler component is automatically created in the cluster.

    1.  Run the following command to query the Deployment:

        ```
        kubectl -n kube-system get deploy |grep cluster-autoscaler
        ```

        The following output is returned:

        ```
        cluster-autoscaler            1/1     1            1           5s
        ```

    2.  Configure the RAM policy that enables the cluster-autoscaler component to access other resource. The sample code is as follows:

        ```
        {
            "Version": "1",
            "Statement": [
                {
                    "Action": [
                        "ess:DescribeScalingGroups",
                        "ess:DescribeScalingInstances",
                        "ess:DescribeScalingActivities",
                        "ess:DescribeScalingConfigurations",
                        "ess:DescribeScalingRules",
                        "ess:DescribeScheduledTasks",
                        "ess:DescribeLifecycleHooks",
                        "ess:DescribeNotificationConfigurations",
                        "ess:DescribeNotificationTypes",
                        "ess:DescribeRegions",
                        "ess:CreateScalingRule",
                        "ess:ModifyScalingGroup",
                        "ess:RemoveInstances",
                        "ess:ExecuteScalingRule",
                        "ess:ModifyScalingRule",
                        "ess:DeleteScalingRule",
                        "ecs:DescribeInstanceTypes",
                        "ess:DetachInstances",
                        "vpc:DescribeVSwitches"
                    ],
                    "Resource": [
                        "*"
                    ],
                    "Effect": "Allow"
                }
            ]
        }
        ```

    3.  Run the following commands to specify the AccessKey pair that is used to grant the RAM policy:

        ```
        export ACCESS_KEY_ID=<ACCESS KEY ID\>
        export ACCESS_KEY_SECRET=<ACCESS KEY SECRET\>
        ```

    4.  Run the following command to create a Secret named alibaba-addon-secret:

        ```
        kubectl -n kube-system create secret generic alibaba-addon-secret --from-literal='access-key-id=${ACCESS_KEY_ID}' --from-literal='access-key-secret=${ACCESS_KEY_SECRET}'
        ```


## Step 2: Create a node pool for auto scaling

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, perform the following steps to go to the **Configure Auto Scaling** page.

    You can go to the Configure Auto Scaling page in the following ways:

    -   Method 1: Find the cluster that you want to manage and choose **More** \> **Auto Scaling** in the **Actions** column.
    -   Method 2:
        1.  Find the cluster that you want to manage and click **Details** in the **Actions** column.
        2.  In the left-side navigation pane, choose **Nodes** \> **Node Pool**.
        3.  In the upper-right corner of the Node Pools page, click **Configure Auto Scaling**.
4.  On the Configure Auto Scaling page, click **Create Node Pool**.

5.  In the Create Node Pool dialog box, set the parameters for the node pool.

    The parameters are described in the following table.

    |Parameter|Description|
    |---------|-----------|
    |**Quantity**|Set the initial number of nodes in the node pool. If you do not want to create nodes in the node pool, set this parameter to 0.|
    |**Operating System**|Select the operating system for the nodes. Valid values: CentOS and Alibaba Cloud Linux 2.1903.|
    |**Node Label**|You can add labels to nodes in the node pool. For example, `workload=auto`.|
    |**ECS Label**|You can add labels to the ECS instances in the node pool.|
    |**Taints**|You can add taints to nodes in the node pool.|
    |**Security Group**|Select the security group to which the nodes belong.|

    For more information about the parameters, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).

6.  Click **OK**.


**Expected result**

Run the following command to verify whether nodes that have the specified **label**, such as `workload=auto`, are added to the cluster:

```
kubectl run nginx --image nginx -l workload=auto
```

