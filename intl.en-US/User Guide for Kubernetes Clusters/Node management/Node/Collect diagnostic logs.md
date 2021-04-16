---
keyword: [ACK, collect the diagnostic logs of multiple nodes, Kubernetes]
---

# Collect diagnostic logs

You can diagnose one or more nodes at a time in the Container Service for Kubernetes \(ACK\) console and collect the diagnostic logs. This topic describes how to collect diagnostic logs with simple steps.

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [Activate OSS](/intl.en-US/Console User Guide/Sign up for OSS.md)

## Authorize nodes to upload diagnostic logs to OSS

Before you can upload diagnostic logs from nodes to an Object Storage Service \(OSS\) bucket, you must grant the nodes the write permissions on the specified directory in OSS. Perform the following steps:

1.  Log on to the [RAM console](https://ram.console.aliyun.com/) by using an Alibaba Cloud account.

2.  In the left-side navigation pane, click **Policies** under **Permissions**.

3.  In the **Policy Name** column, click the name of the target custom policy.

    **Note:** System policies and custom policies are available in Alibaba Cloud Resource Access Management \(RAM\). System policies can be viewed but cannot be modified. However, custom policies can be created, viewed, and modified.

4.  On the **Policy Document** tab, click **Modify Policy Document**.

5.  In the **Modify Policy Document** pane, add the following content, and enter the bucket name and directory.

    ![Modify the policy](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8735359951/p127244.png)

    ```
    {
        "Action": [
            "oss:GetBucket",
            "oss:PutObject",
            "oss:GetObject"
        ],
        "Resource": [
            "acs:oss:*:*:<the OSS bucket name>/<the directory used to store diagnostic logs>/*"
        ],
        "Effect": "Allow"
    },
    {
        "Action": [
            "oss:GetBucketInfo"
        ],
        "Resource": [
            "acs:oss:*:*:<the OSS bucket name>"
        ],
        "Effect": "Allow"
    }
    ```

6.  Click **OK**.


## Select nodes to diagnose

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node**.

5.  In the left-side navigation pane, choose **Clusters** \> **Nodes**.

6.  On the Nodes page, select one or more nodes, and click **Node Diagnosis**.

7.  In the Node Diagnosis dialog box, perform the following operations:

    -   Select **Upload to OSS** and perform the following steps:

        You can upload the diagnostic logs to an OSS bucket. Before you perform the following steps, you must grant the nodes the write permissions on the specified directory in OSS. This allows the nodes to upload the node diagnostic logs to the specified OSS bucket. For more information, see [Authorize nodes to upload diagnostic logs to OSS](#section_zq3_ob1_8gb).

        1.  Enter the name and directory of the OSS bucket.

            For example, if you want to upload diagnostic logs to /acs/diagnose directory in the myBucket, enter myBucket/acs/diagnose.

        2.  Select or clear **Share Diagnosis Logs**.

            If you select the check box, the system generates a temporary link for you to download the diagnostic logs. You can also share the link to the ACK support team to request technical support.

        3.  Click **OK**.

            After the preceding steps are complete, you can obtain the **task ID** of Cloud Assistant in the console. Then, you can use the task ID to find and view the logs of diagnostic script executions in the Elastic Compute Service \(ECS\) Cloud Assistant console.

        4.  In the Node Diagnosis dialog box, click **Go to Cloud Assistant to View Diagnosis Script Execution Logs**.
        5.  On the Cloud Assistant page, click the **Command Execution Result** tab.
        6.  Find the **Task ID** and click **View** in the Actions column.

            You can log on to the [OSS console](https://partners-intl.console.aliyun.com/#/oss) to view the diagnostic logs that are collected in the specified directory.

    -   Clear **Upload to OSS**, and click **OK**.

        If you clear **Upload to OSS**, you do not need to grant the nodes the write permissions on the specified directory in OSS. You can log on to[ECS Console](https;//parnters-intl.console.aliyun.com/#/ecs) and choose **Maintenance and Monitoring** \> **ECS Cloud Assistant** \> **Command Execution Result** to view relevant diagnostic logs.


