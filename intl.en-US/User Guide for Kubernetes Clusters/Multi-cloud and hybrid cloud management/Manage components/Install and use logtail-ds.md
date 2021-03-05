---
keyword: [logtail-ds, Log Service, install logtail-ds]
---

# Install and use logtail-ds

Container Service for Kubernetes \(ACK\) clusters are integrated with Log Service. When you create an ACK cluster, you can enable Log Service by installing the logtail-ds component. After Log Service is enabled, it collects logs from the containers of the ACK cluster. The logs include the standard output and text files from the containers. This topic describes how to install and use logtail-ds.

## Step 1: Set permissions

1.  Create a permission policy.

    For more information, see [Create a custom policy](/intl.en-US/Policy Management/Custom policies/Create a custom policy.md).

    The following code block shows the content of the permission policy for logtail-ds:

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


Run the following command to create the Secret that is required to use logtail-ds:

```
kubectl -n kube-system create secret generic alibaba-addon-secret --from-literal='access-key-id=<your access key id>' --from-literal='access-key-secret=<your access key secret>'
```

**Note:**

Replace `<your access key id>` and `<your access key secret>` in the preceding command with the AccessKey information that you have obtained.

## Step 2: Install logtail-ds

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  On the Clusters page, find the cluster that you want to manage and choose **More** \> **Manage System Components** in the **Actions** column.

3.  On the Add-ons page, find the **logtail-ds** component and click **Install** for the component.


## Step 3: Enable Log Service

For more information, see [Use Log Service to collect container logs](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Use Log Service to collect container logs.md).

**Related topics**  


[Prerequisites](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Manage components/Prerequisites.md)

