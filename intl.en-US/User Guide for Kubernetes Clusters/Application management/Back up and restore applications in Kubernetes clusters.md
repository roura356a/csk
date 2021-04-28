---
keyword: [back up applications, restore applications, restore applications across clusters]
---

# Back up and restore applications in Kubernetes clusters

Container Service for Kubernetes \(ACK\) integrates open source Velero that allows you to back up and restore applications. This allows you to back up and restore applications and volumes in Kubernetes clusters and registered external Kubernetes clusters. You can create and restore backups in the same cluster, or create backups in one cluster and restore the backups to another cluster. This topic describes how to back up and restore applications in Kubernetes clusters. This helps protect your applications from service interruptions and data loss.

-   This feature is in public preview. To use this feature, you must [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to apply for the feature to be enabled on your account.
-   A Kubernetes cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md), [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md), and [Create a cluster registration proxy and register an on-premises cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of registered clusters/Create a cluster registration proxy and register an on-premises cluster.md).

    **Note:** The Kubernetes version of your cluster must be later than V1.10. Otherwise, the feature may not function as expected.

-   An Object Storage Service \(OSS\) bucket is created. For more information, see [Create buckets](/intl.en-US/Console User Guide/Manage buckets/Create buckets.md).

The number of enterprises that choose to deploy their applications on Kubernetes are increasing. Therefore, regular application backups are important. You can use application backups to restore applications that cannot be recovered after the applications are accidentally disrupted for a long period of time. Different from the traditional single-server backup and disk backup, the application backup feature is used to back up applications and the relevant data, resource objects, and configurations. You can also use this feature to back up all resources in a namespace.

## Limits

-   You can back up applications only by namespace.
-   You can create only full backups for applications. Incremental backups are not supported.
-   When you back up an application, the resources that are being deleted are not backed up.

## Step 1: Grant OSS permissions to the cluster

**Grant OSS permissions to managed or dedicated Kubernetes clusters**

If you use managed or dedicated Kubernetes clusters, you must grant OSS permissions to your clusters.

1.  Create a custom permission policy with the required OSS permissions. For more information, see [Create a custom policy](/intl.en-US/Policy Management/Custom policies/Create a custom policy.md).

    **Note:** For information about how to regulate fine-grained access to OSS, see [Use RAM to manage OSS permissions](/intl.en-US/Tutorials/Use RAM to manage OSS permissions.md).

    To grant full OSS permissions, create a permission policy based on the following template:

    ```
    {
        "Version": "1",
        "Statement": [
            {
                "Action": [
                    "oss:PutObject",
                    "oss:GetObject",
                    "oss:DeleteObject",
                    "oss:GetBucket",
                    "oss:ListObjects",
                    "oss:ListBuckets"
                ],
                "Resource": [
                    "*"
                ],
                "Effect": "Allow"
            }
        ]
    }
    ```

    To grant only read and write permissions on a specified OSS bucket, create a permission policy based on the following template:

    ```
    {
        "Version": "1",
        "Statement": [
            {
                "Action": [
                    "oss:PutObject",
                    "oss:GetObject",
                    "oss:DeleteObject",
                    "oss:GetBucket",
                    "oss:ListObjects",
                    "oss:ListBuckets"
                ],
                "Resource": [
                    "acs:oss:*:*:mybackups",
                    "acs:oss:*:*:mybackups/*"
                ],
                "Effect": "Allow"
            }
        ]
    }
    ```

    Replace the value of `mybackups` with the name of the OSS bucket that you have created.

2.  Grant permissions to the Resource Access Management \(RAM\) role of the managed Kubernetes cluster.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  On the details page of the cluster, click the **Cluster Resources** tab and view the worker RAM role of the cluster.

    5.  Log on to the [RAM console](https://ram.console.aliyun.com/).

    6.  In the left-side navigation pane, click **Grants**.

    7.  On the Grants page, click **Grant Permission**. In the **Add Permissions** panel, set the parameters and click **OK**.

        |Parameter|Description|
        |---------|-----------|
        |Authorized Scope|Valid values: **Alibaba Cloud Account** and **Specific Resource Group**.|
        |Principal|Enter the worker RAM role that you previously obtained.|
        |Select Policy|Click **Custom Policy**, enter the name of the permission policy that is created in Step [1](#step_csd_xek_3x4), and then click the name of the policy.|


**Grant permissions to a registered Kubernetes cluster**

If your applications run in a registered Kubernetes cluster, you must create a RAM user for the cluster. Then, create an AccessKey pair for the RAM user and grant the RAM user permissions to access cloud resources.

1.  Create a RAM user. For more information, see [Create a RAM user](/intl.en-US/RAM User Management/Create a RAM user.md).

2.  Create a custom permission policy with the required OSS permissions. For more information, see Step [1](#step_csd_xek_3x4).

3.  Grant permissions to the RAM user. For more information, see [Grant permissions to a RAM user](/intl.en-US/RAM User Management/Grant permissions to a RAM user.md).

4.  Create an AccessKey pair for the RAM user. For more information, see [Obtain an AccessKey pair]().

5.  Create a Secret in the registered Kubernetes cluster.

    To ensure that the AccessKey pair is used only within the registered cluster, you must use the AccessKey pair to create a Secret named alibaba-addon-secret in the cluster. This reduces the risk of information leakage.

    ACK installs migrate-controller in the velero namespace, which is inherited from the open source Velero project. If the namespace does not exist, you must create a namespace that is named velero. After you create the namespace, use the AccessKey pair to create a Secret that is named alibaba-addon-secret in the namespace.

    1.  Run the following command to create a namespace named velero:

        ```
        kubectl create ns velero
        ```

    2.  Run the following command to create a Secret named alibaba-addon-secret:

        ```
        kubectl -n velero create secret generic alibaba-addon-secret --from-literal='access-key-id=<your AccessKey ID>' --from-literal='access-key-secret=<your AccessKey Secret>'
        ```

        Replace `your AccessKey ID` and `your AccessKey Secret` with the AccessKey ID and AccessKey secret that are obtained in Step [4](#step_o2f_lop_eli).


## Step 2: Install the application backup component

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Application Backup \(Public Preview\)**.

5.  On the Application Backup page, click **Install**.

    **Note:** If the velero namespace does not exist, the system automatically creates a namespace named velero when it installs the component. Do not delete this namespace when you back up applications.

    If the page appears, as shown in the following figure, it indicates that the component is installed.

    ![Application backup component](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2337284161/p214169.png)


## Step 3: Create a backup vault

When you back up applications in an ACK cluster, the backup data is stored in an OSS bucket. The following content describes how to access an OSS bucket:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Application Backup \(Public Preview\)**.

5.  On the Application Backup page, click **Create** on the **Backup Vaults** tab.

6.  In the **Create** panel, set the parameters and click **OK**.

    |Parameter|Description|
    |---------|-----------|
    |Name|The name of the backup vault. The name can contain lowercase letters and digits.|
    |Bucket Region|The region where the OSS bucket is deployed.|
    |Bucket Name|The name of the OSS bucket.|
    |Bucket Subdirectory|The subdirectory of the OSS bucket. This parameter is optional.|
    |Network Type|Valid values: **Public Network** and **Internal Network**. If you can transmit data through the internal endpoint of the OSS bucket, select the internal network type. By default, ACK transmits data over the Internet.|


## Step 4: Create a backup task

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Application Backup \(Public Preview\)**.

5.  On the Application Backup page, click the **Backups** tab.

6.  On the **Backups** tab, click **Create**.

7.  If you want to back up a volume, run the following command to add an annotation to the pod to which the volume is mounted:

    **Note:** If you want to back up an application, skip this step.

    ```
    kubectl -n <The name of the volume that you want to back up> annotate pod/<The name of the pod> backup.velero.io/backup-volumes=<The name of the persistent volume claim (PVC) that corresponds to the pod>
    ```

8.  In the **Create** dialog box, set **Name**, **Backup Vaults**, **Backup Namespace**, **Label**, **Excluded Resources**, **Validity Period**, and **Scheduled Backup**. Click **OK**.

    ![Create a backup](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0795159161/p245376.png)

    **Note:**

    -   The name can contain only lowercase letters and digits.
    -   You must set at least one of the following parameters: **Backup Namespace** and **Label**.
    -   The backup schedule. You can enter a cron expression or a time interval.
    -   After scheduled backup is enabled, you can view the scheduled backup configurations of this backup on the **Scheduled Backup Configurations** tab of the Application Backup page.
    On the **Backups** tab, if the state of the backup task is **Completed**, the task is created.


## Step 5: Create a restoration task

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Application Backup \(Public Preview\)**.

5.  On the Application Backup page, click the **Restore** tab.

6.  On the **Restore** tab, click **Create Restoration Task**.

7.  In the **Create Restoration Task** panel, enter a name in the **Name** field, select a backup from the **Backup Name** drop-down list, and then click **OK**.

    **Note:**

    -   The name can contain only lowercase letters and digits.
    -   Existing resources in the cluster are not overwritten during restoration. Only resources that do not exist in the cluster are restored. If you want to restore an application to an earlier version, you must first delete the current resources of the application.
8.  In the message that appears, click **OK**.


## Restore an application across clusters

You must create two clusters before you can restore an application that is backed up in a cluster to another cluster. In this example, a cluster named cluster\_A and the other named cluster\_B are created. The following example shows how to back up an application in cluster\_A and restore the application in cluster\_B.

**Note:** Make sure that the two clusters use the same Kubernetes version. Otherwise, you cannot restore the application in cluster\_B.

![Architecture](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1135242161/p223924.png)

1.  Grant the required OSS permissions to cluster\_A and cluster\_B. For more information, see [Step 1: Grant OSS permissions to the cluster](#section_eex_psy_s4t).

2.  Install the application backup component in cluster\_A and cluster\_B. For more information, see [Step 2: Install the application backup component](#section_7v1_q3t_hak).

3.  Create backup vaults for cluster\_A and cluster\_B. When you create backup vaults for cluster\_A and cluster\_B, select the same OSS bucket and set Network Type to Public Network. For more information, see [Step 3: Create a backup vault](#section_jcd_lxp_dpg).

4.  Create a backup task in cluster\_A. For more information, see [Step 4: Create a backup task](#section_v4n_i9q_da6).

    After the backup task is created, you can log on to cluster\_B to view the backup task on the **Backups** tab of the Application Backup page.

5.  On the Application Backup page, click the **Restore** tab, find the backup that is created in cluster\_A, and then restore the backup. For more information, see [Step 5: Create a restoration task](#section_gdu_9n6_lft).


**Related topics**  


[Use migrate-controller to back up and restore applications](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Switch traffic to the Kubernetes application/Use migrate-controller to back up and restore applications.md)

