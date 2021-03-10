---
keyword: [ACK, collect the diagnostic logs of multiple nodes, Kubernetes]
---

# Collect diagnostic logs

You can diagnose one or more nodes at a time in the Container Service for Kubernetes \(ACK\) console and collect the diagnostic logs. This topic describes how to collect diagnostic logs by using simple steps.

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [Activate OSS](/intl.en-US/Console User Guide/Sign up for OSS.md)

## Authorize nodes to upload diagnostic logs to OSS

Before you can upload diagnostic logs from nodes to an Object Storage Service \(OSS\) bucket, you must grant the nodes the write permissions on the specified directory in OSS. Perform the following steps:

1.  Log on to the [RAM console](https://ram.console.aliyun.com/) by using an Alibaba Cloud account.

2.  In the left-side navigation pane, click **Policies** under **Permissions**.

3.  In the **Policy Name** column, click the name of the target custom policy.

    **Note:** System policies and custom policies are available in Alibaba Cloud Resource Access Management \(RAM\). System policies can be viewed but cannot be modified. However, custom policies can be created, viewed, and modified.

4.  On the **Policy Document** tab, click **Modify Policy Document**.

5.  In the **Modify Policy Document** pane, add the following content to the Policy Document section, and enter the bucket name and directory.

    ![Modify the policy](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8735359951/p127244.png)

    ```
    {
        "Action": [
            "oss:GetBucket",
            "oss:PutObject",
            "oss:GetObject"
        ],
        "Resource": [
            "acs:oss:*:*:<OSS bucket name>/<Directory used to store diagnostic logs>/*"
        ],
        "Effect": "Allow"
    },
    {
        "Action": [
            "oss:GetBucketInfo"
        ],
        "Resource": [
            "acs:oss:*:*:<Bucket name>"
        ],
        "Effect": "Allow"
    }
    ```


## Select the nodes for diagnostics

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, choose **Clusters** \> **Nodes**.

5.  On the Nodes page, select one or more nodes, and click **Node Diagnosis**.

6.  In the Node Diagnosis dialog box, perform the following operations:

    -   Select **Upload to OSS**, and perform the following steps:

        After you select the check box, you can upload diagnostic logs to an OSS bucket. Before you perform the following steps, you must grant the nodes the write permissions on the specified directory in OSS. This allows the nodes to upload the node diagnostic logs to the specified OSS bucket. For more information, see [Authorize nodes to upload diagnostic logs to OSS](#section_zq3_ob1_8gb).

        1.  Enter the name and directory of the OSS bucket.

            For example, if you want to upload node diagnostic logs to myBucket in the /acs/diagnose directory, enter myBucket/acs/diagnose.

        2.  Select or clear **Share Diagnosis Logs**.

            After you select the check box, the system generates a temporary link for the collected diagnosis logs. You can click the link to download the logs, or share the link with the ACK support team to request technical support.

        3.  Click **OK**.

            After the preceding steps are completed, you can obtain the task ID of Cloud Assistant in the console. You can then use the task ID to find and view the logs of diagnosis script executions in the Elastic Compute Service \(ECS\) console.

        4.  In the Node Diagnosis dialog box, click **Go to Cloud Assistant to View Diagnosis Script Execution Logs**.
        5.  On the Cloud Assistant page, click the **Tasks** tab.
        6.  Find the task ID that you want to use to view logs and click **View**.

            You can view the collected diagnostic log files in the specified directory in the [OSS console](https://partners-intl.console.aliyun.com/#/oss).

    -   Clear **Upload to OSS** and click **OK**.

        If you clear **Upload to OSS**, you do not need to grant the nodes the write permissions on the specified directory in OSS. To view the collected diagnostic logs, log on to the [ECS console](https;//parnters-intl.console.aliyun.com/#/ecs). In the left-side navigation pane, choose **Maintenance & Monitoring** \> **Send Remote Commands \(Cloud Assistant\)**, and click the **Tasks** tab.


