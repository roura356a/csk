---
keyword: [event center, registered external cluster]
---

# Create a Kubernetes event center for an external Kubernetes cluster

You can create a Kubernetes event center to record events of an external Kubernetes cluster. These events include changes to pod configurations and component exceptions. The Kubernetes event center collects, stores, and visualizes cluster events in real time. The event center allows you to query and analyze the events and configure alerts. This topic describes how to create a Kubernetes event center for a registered external Kubernetes cluster.

An external Kubernetes cluster is registered in the ACK console. For more information, see [Create a cluster registration proxy and register an on-premises cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of external clusters/Create a cluster registration proxy and register an on-premises cluster.md).

Kubernetes is designed based on the state machine. Events are generated due to transitions between different states. Typically, Normal events are generated when the state machine changes to expected states and Warning events are generated when the state machine changes to unexpected states.

Container Service for Kubernetes \(ACK\) provides out-of-the-box monitoring solutions for events in different scenarios. The node-problem-detector and kube-eventer tools that are maintained by ACK allow you to monitor Kubernetes events.

![Event monitoring workflow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7784133261/p272466.png)

-   node-problem-detector is a tool for diagnosing Kubernetes nodes. node-problem-detector detects node exceptions, generates node events, and works with kube-eventer to trigger alerts for these events. node-problem-detector generates node events when the following exceptions are detected: Docker engine hangs, Linux kernel hangs, outbound traffic errors, and file descriptor errors. For more information, see [NPD](https://github.com/AliyunContainerService/node-problem-detector).
-   kube-eventer is an open source event emitter that is maintained by ACK. kube-eventer sends Kubernetes events to sinks such as DingTalk, Log Service, and EventBridge. kube-eventer also provides filter conditions to filter different levels of events. You can use kube-eventer to collect events in real time, trigger alerts upon specific events, and asynchronously archive events. For more information, see [kube-eventer](https://github.com/AliyunContainerService/kube-eventer).

## Step 1: Configure RAM permissions for the event center component

Before you can install the component in an external cluster, you must set the AccessKey pair to grant the external cluster the permissions to access Alibaba Cloud resources. Before you set the AccessKey pair, create a Resource Access Management \(RAM\) user and grant the RAM user the permissions to access Alibaba Cloud resources.

1.  Create a RAM user. For more information, see [Create a RAM user](/intl.en-US/RAM User Management/Basic operations/Create a RAM user.md).

2.  Create a permission policy. For more information, see [Create a custom policy](/intl.en-US/Policy Management/Custom policies/Create a custom policy.md).

    The following code block shows the content of the permission policy for the Logtail component:

    ```
    {
        "Version": "1",
        "Statement": [
            {
                "Action": [
                    "log:CreateProject",
                    "log:GetProject",
                    "log:DeleteProject",
                    "log:CreateLogStore",
                    "log:GetLogStore",
                    "log:UpdateLogStore",
                    "log:DeleteLogStore",
                    "log:CreateConfig",
                    "log:UpdateConfig",
                    "log:GetConfig",
                    "log:DeleteConfig",
                    "log:CreateMachineGroup",
                    "log:UpdateMachineGroup",
                    "log:GetMachineGroup",
                    "log:DeleteMachineGroup",
                    "log:ApplyConfigToGroup",
                    "log:GetAppliedMachineGroups",
                    "log:GetAppliedConfigs",
                    "log:RemoveConfigFromMachineGroup",
                    "log:CreateIndex",
                    "log:GetIndex",
                    "log:UpdateIndex",
                    "log:DeleteIndex",
                    "log:CreateSavedSearch",
                    "log:GetSavedSearch",
                    "log:UpdateSavedSearch",
                    "log:DeleteSavedSearch",
                    "log:CreateDashboard",
                    "log:GetDashboard",
                    "log:UpdateDashboard",
                    "log:DeleteDashboard",
                    "log:CreateJob",
                    "log:GetJob",
                    "log:DeleteJob",
                    "log:UpdateJob",
                    "log:PostLogStoreLogs",
                    "log:CreateSortedSubStore",
                    "log:GetSortedSubStore",
                    "log:ListSortedSubStore",
                    "log:UpdateSortedSubStore",
                    "log:DeleteSortedSubStore",
                    "log:CreateApp",
                    "log:UpdateApp",
                    "log:GetApp",
                    "log:DeleteApp",
                    "cs:DescribeTemplates",
                    "cs:DescribeTemplateAttribute"
                ],
                "Resource": [
                    "*"
                ],
                "Effect": "Allow"
            }
        ]
    }
    ```

3.  Grant permissions to the RAM user. For more information, see [Grant permissions to a RAM user](/intl.en-US/RAM User Management/Authorization management/Grant permissions to a RAM user.md).

    You can create a custom permission policy or select the AliyunECIFullAccess permission policy to authorize the RAM user.

4.  Create an AccessKey pair for the RAM user. For more information, see [Obtain an AccessKey pair]().

5.  Use the AccessKey pair to create a Secret named `alibaba-addon-secret` in the registered external cluster.

    The system automatically uses the AccessKey pair to access cloud resources when you install the components of the event center.

    Run the following command to create the Secret. The Logtail component uses the Secret.

    ```
    kubectl -n kube-system create secret generic alibaba-addon-secret --from-literal='access-key-id=<your AccessKey ID>' --from-literal='access-key-secret=<your AccessKey Secret>'
    ```

    **Note:** Replace `<your AccessKey ID>` and `<your AccessKey Secret>` with the AccessKey pair that you obtained.


## Step 2: Install the Kubernetes event center

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  Choose **Operations** \> **Event Center**.

5.  If the **ack-node-problem-detector is not installed** message appears, click **Create Event Center**.

6.  In the upper-right corner, click **Cluster Events Management**. In the left-side navigation pane of the **K8s Event Center** page, find the cluster that you want to manage. Then, click the ![Show](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5301471161/p203800.png) icon to the left of the cluster name to view details of the Kubernetes event center.

    The Kubernetes event center provides event overviews, event details, and information about pod lifecycles. You can also customize queries and configure alerts.

    ![Event overview](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0639340261/p243385.png)


After the configuration is successful, you can use the Kubernetes event center. For more information, see [Create and use a Kubernetes event center](/intl.en-US/Application/K8s Event Center/Create and use a Kubernetes event center.md).

After the Kubernetes event center is created for the external Kubernetes cluster, you can use the event center to check event overviews, view event details, check pod lifecycles, configure alerts, and customize queries.

**Related topics**  


[Overview of registered clusters](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of external clusters/Overview of registered clusters.md)

[Event monitoring](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/Event monitoring.md)

