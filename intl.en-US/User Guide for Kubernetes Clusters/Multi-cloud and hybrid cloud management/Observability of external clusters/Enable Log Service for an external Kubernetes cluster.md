---
keyword: [registered external cluster, enable Log Service]
---

# Enable Log Service for an external Kubernetes cluster

You can enable Log Service for external Kubernetes clusters that are registered in the Container Service for Kubernetes \(ACK\) console. This way, you can manage Kubernetes clusters that are deployed across regions in a centralized manner. This topic describes how to enable Log Service for a registered external Kubernetes cluster.

An external Kubernetes cluster is registered in the ACK console. For more information, see [Create a cluster registration proxy and register an on-premises cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of external clusters/Create a cluster registration proxy and register an on-premises cluster.md).

## Step 1: Configure RAM permissions for the Log Service component

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

4.  Create an AccessKey pair for the RAM user. For more information, see [Obtain an AccessKey pair]().

5.  Use the AccessKey pair to create a Secret named `alibaba-addon-secret` in the registered external cluster.

    Run the following command to create the Secret. The Logtail component uses the Secret.

    ```
    kubectl -n kube-system create secret generic alibaba-addon-secret --from-literal='access-key-id=<your AccessKey ID>' --from-literal='access-key-secret=<your AccessKey Secret>'
    ```

    **Note:** Replace `<your AccessKey ID>` and `<your AccessKey Secret>` with the AccessKey pair that you obtained.


## Step 2: Install the logtail-ds component

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and choose **More** \> **Manage System Components** in the **Actions** column.

4.  Click the Logs and Monitoring tab. Find the **logtail-ds** component and click **Install**.

5.  In the Note dialog box, click **OK**.


## Step 3: Configure Log Service

For more information about how to configure Log Service when you create an application, see [Step 2: Configure Log Service when you create an application](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Collect log files from containers by using Log Service.md).

**Related topics**  


[Overview of registered clusters](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of external clusters/Overview of registered clusters.md)

