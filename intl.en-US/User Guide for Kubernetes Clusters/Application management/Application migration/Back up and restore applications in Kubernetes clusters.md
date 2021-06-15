---
keyword: [back up applications, restore applications, restore applications across clusters]
---

# Back up and restore applications in Kubernetes clusters

Container Service for Kubernetes \(ACK\) is integrated with open source Velero to provide the application backup feature. This feature allows you to back up and restore applications and volumes in ACK clusters and registered external clusters. You can create and restore backups in the same cluster. You can also create backups in one cluster and restore backups to another cluster. This topic describes how to back up and restore applications in Kubernetes clusters. This helps protect your applications from service interruptions and data loss.

-   This feature is in public preview. To use this feature, you must [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to apply for the feature to be enabled on your account.
-   A Kubernetes cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md), [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md), or [Create a cluster registration proxy and register an on-premises cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of external clusters/Create a cluster registration proxy and register an on-premises cluster.md).

    **Note:** The Kubernetes version of your cluster must be later than V1.10. Otherwise, the feature may not function as expected.

-   An Object Storage Service \(OSS\) bucket is created. For more information, see [Create buckets](/intl.en-US/Console User Guide/Manage buckets/Create buckets.md).

The number of enterprises that choose to deploy their applications on Kubernetes is increasing. Therefore, regular application backups are important. You can use application backups to restore applications that cannot be recovered if the applications are disrupted for a long period of time. Traditional backup solutions include single-server backups and disk backups. Compared with traditional solutions, application backups allow you to back up applications and the data, resource objects, configurations, and namespaces of the applications.

The application backup feature supports only OSS as the storage of application backups. Before you use OSS, you must grant OSS permissions to your cluster.

## Limits

-   You can create only full backups for applications. Incremental backups are not supported.
-   When you back up an application, the resources in the Deleting state are not backed up.
-   If you want to back up a disk volume, you must install the Container Storage Interface \(CSI\) plug-in of V1.1.0 or later. For more information about how to install the CSI plug-in, see [t1592079.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Install and upgrade the CSI plug-in.md).

## Step 1: Grant OSS permissions to the cluster

**Configure permissions for managed or dedicated Kubernetes clusters**

If you use a managed or dedicated Kubernetes cluster, you must grant the required OSS permissions to the cluster.

1.  Create a custom permission policy with the required OSS permissions. For more information, see [Create a custom policy](/intl.en-US/Policy Management/Custom policies/Create a custom policy.md).

    **Note:** For more information about how to regulate fine-grained access to OSS, see [Use RAM to manage OSS permissions](/intl.en-US/Tutorials/Use RAM to manage OSS permissions.md).

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

    Replace `mybackups` with the name of the OSS bucket that you have created.

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

If your applications are deployed in a registered Kubernetes cluster, you must create a RAM user for the cluster. Grant the RAM user the permissions to access other cloud resources and create an AccessKey pair for the RAM user.

1.  Create a RAM user. For more information, see [Create a RAM user](/intl.en-US/RAM User Management/Basic operations/Create a RAM user.md).

2.  Create a custom permission policy with the required OSS permissions. For more information, see Step [1](#step_csd_xek_3x4).

3.  Grant permissions to the RAM user. For more information, see [Grant permissions to a RAM user](/intl.en-US/RAM User Management/Authorization management/Grant permissions to a RAM user.md).

4.  Create an AccessKey pair for the RAM user. For more information, see [Obtain an AccessKey pair]().

5.  Create a Secret in the registered Kubernetes cluster.

    To ensure that the AccessKey pair is used only within the registered Kubernetes cluster, you must use the AccessKey pair to create a Secret named alibaba-addon-secret in the cluster. This reduces the risk of information leakage.

    ACK installs migrate-controller in the velero namespace, which is inherited from the open source Velero project. If the namespace does not exist, you must create a namespace that is named velero. After you create the namespace, use the AccessKey pair to create a Secret that is named alibaba-addon-secret in the namespace.

    1.  Run the following command to create a namespace named **velero**:

        ```
        kubectl create ns velero
        ```

    2.  Run the following command to create a Secret named alibaba-addon-secret:

        ```
        kubectl -n velero create secret generic alibaba-addon-secret --from-literal='access-key-id=<your AccessKey ID>' --from-literal='access-key-secret=<your AccessKey Secret>'
        ```

        Replace `your AccessKey ID` and `your AccessKey Secret` with the AccessKey ID and AccessKey secret that are obtained in Step [4](#step_o2f_lop_eli).


## Step 2: Install the application backup component

**Note:** To use the application backup feature, you must first install the application backup component. If you have installed the application backup component, skip this step.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Application Backup \(Public Preview\)**.

5.  On the Application Backup page, click **Install**.

    **Note:** If the velero namespace does not exist, the system automatically creates a namespace named velero when it installs the component. Do not delete this namespace when you back up applications.

    If the page appears, as shown in the following figure, it indicates that the component is installed.

    ![Application backup component](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2337284161/p214169.png)


## Step 3: Create a backup vault

When you back up applications in an ACK cluster, the backup data is stored in an OSS bucket. The following content describes how to access an OSS bucket:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Application Backup \(Public Preview\)**.

5.  On the Application Backup page, click **Create** on the **Backup Vaults** tab.

6.  In the **Create** panel, set the parameters and click **OK**.

    |Parameter|Description|
    |---------|-----------|
    |Vault Name|The name of the backup vault. The name can contain lowercase letters and digits.|
    |Bucket Region|The region where the OSS bucket is deployed.|
    |Bucket Name|The name of the OSS bucket.|
    |Bucket Subdirectory|The subdirectory of the OSS bucket. This parameter is optional.|
    |Network Type|Valid values: **Public Network** and **Internal Network**. If you can transmit data through the internal endpoint of the OSS bucket, select the internal network type. By default, ACK transmits data over the Internet.|


## Step 4: Create a backup task

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Application Backup \(Public Preview\)**.

5.  On the Application Backup page, click the **Backups** tab.

6.  On the **Backups** tab, click **Create**.

7.  In the **Create** panel, set the parameters and click **OK**.

    ![Create a backup](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0795159161/p245376.png)

    |Parameter|Description|
    |---------|-----------|
    |**Name**|The name of the backup task. This parameter is required.|
    |**Backup Vaults**|Select the backup vault that you want to use. This parameter is required.|
    |**Backup Namespaces**|You can select one or more namespaces that you want to backup. Applications in the selected namespaces are backed up.|
    |**Label**|Specify a label. Applications that are added with this label are backed up.|
    |**Excluded Namespaces**|You can select one or more namespaces that you want to exclude from the backup task. Applications in the selected namespaces are not backed up.|
    |**Excluded Resources**|You can specify multiple types of Kubernetes resources, such as `pod or Secret`, that you want to exclude from the backup. Separate resource types with commas \(,\). Resources of the selected types are not backed up.|
    |**Volume Backup**|Specify whether to enable volume backup to back up application data. If you select Enable, you must set **Backup Method**:

    -   **Disk Snapshot**: uses the disk snapshot feature of Alibaba Cloud disks to back up application data. The backup data is stored in a backend disk.
    -   **Restic**: uses the open source Restic project to back up application data. The backup data is stored in OSS. |
    |**Validity Period**|Specify the validity period of the backup data. Backup data cannot be restored after it expires.|
    |**Scheduled Backup**|Specify whether to enable scheduled backup for applications and application data. You can enter a cron expression to specify the backup schedule. For more information about cron expressions, see [how-use-cron-linux](https://opensource.com/article/17/11/how-use-cron-linux).|

    **Note:**

    -   The backup name can contain only lowercase letters and digits, and cannot contain spaces.
    -   You can select multiple backup namespaces and multiple excluded namespaces.
    -   If you want to back up resources by label, you can specify only one label.
    -   The Disk Snapshot option uses the disk snapshot feature of Alibaba Cloud disks to back up a disk volume. The Restic option uses the open source Restic project to back up a disk volume. We recommend that you use the Disk Snapshot option.
    -   When you specify the backup schedule, you can enter a cron expression or a time interval.
    -   If you enable scheduled backup, you can view the details of scheduled backups on the **Scheduled Backup Configurations** tab of the Application Backup page.
    On the **Backups** tab, if the state of the backup task is **Completed**, the task is created.


## Step 5: Create a restoration task

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Application Backup \(Public Preview\)**.

5.  On the Application Backup page, click the **Restore** tab.

6.  On the **Restore** tab, click **Create Restoration Task**.

7.  In the **Create Restoration Task** panel, enter a name in the **Name** field, select a backup from the **Backup Name** drop-down list, and then click **OK**.

    **Note:**

    -   The name can contain only lowercase letters and digits.
    -   Existing resources in the cluster are not overwritten during restoration. Only resources that do not exist in the cluster are restored. You must delete the current resources of an application before you can restore the application to an earlier version.
8.  In the message that appears, click **OK**.


## Restore an application across clusters

You must create two clusters before you can restore an application across clusters. In this example, a cluster named cluster\_A and the other named cluster\_B are used. The following example shows how to back up an application in cluster\_A and restore the application in cluster\_B.

**Note:** Make sure that the two clusters use the same Kubernetes version. Otherwise, you cannot restore the application in cluster\_B.

![Backup architecture](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1135242161/p223924.png)

1.  Grant the required OSS permissions to cluster\_A and cluster\_B. For more information, see [Step 1: Grant OSS permissions to the cluster](#section_eex_psy_s4t).

2.  Install the application backup component in cluster\_A and cluster\_B. For more information, see [Step 2: Install the application backup component](#section_7v1_q3t_hak).

3.  Create backup vaults for cluster\_A and cluster\_B. When you create backup vaults for cluster\_A and cluster\_B, select the same OSS bucket and set Network Type to Public Network. For more information, see [Step 3: Create a backup vault](#section_jcd_lxp_dpg).

4.  Create a backup task in cluster\_A. For more information, see [Step 4: Create a backup task](#section_v4n_i9q_da6).

    After the backup task is created, you can log on to cluster\_B to view the backup task on the **Backups** tab of the Application Backup page.

5.  On the Application Backup page, click the **Restore** tab and create a restoration task based on the backup task that is created in cluster\_A. For more information, see [Step 5: Create a restoration task](#section_gdu_9n6_lft).


**Related topics**  


[Use migrate-controller to back up and restore applications](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Application migration/Use migrate-controller to back up and restore applications.md)

