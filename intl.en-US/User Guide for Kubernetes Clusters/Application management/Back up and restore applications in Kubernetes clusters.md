---
keyword: [back up applications, restore applications, restore applications to a specified cluster]
---

# Back up and restore applications in Kubernetes clusters

Container Service for Kubernetes \(ACK\) integrates the open source tool Velero that allows you to back up and restore applications. ACK allows you to back up and restore applications and volumes in Kubernetes clusters and registered clusters. You can create and restore backups in the same cluster, or create backups in one cluster and restore backups to another cluster. This topic describes how to back up and restore application in Kubernetes clusters. This protects your applications from service interruption and data corruption.

-   This feature is in public preview. To use this feature, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to be included in the whitelist.
-   A Kubernetes cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md) or [Register an external Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Register an external Kubernetes cluster.md).

    **Note:** Make sure that the version of your Kubernetes cluster is 1.10 or later. Otherwise, this feature may not function as expected.

-   An Object Storage Service \(OSS\) bucket is created. For more information, see [Create buckets](/intl.en-US/Console User Guide/Manage buckets/Create buckets.md).

A growing number of applications are running on Kubernetes. Therefore, it is important to back up applications periodically. You can use backups to restore applications when applications cannot recover after they are accidentally interrupted for a long period of time. Traditional backup solutions include single-server backups and disk backups. Compared with traditional solutions, application backups allow you to back up applications, including their data, resource objects, configurations, and namespaces.

## Limits

-   You can back up applications only by namespace.
-   You can create only full backups for applications. Incremental backups are not supported.
-   When you back up an application, the resources in the Deleting state are not backed up.

## Step 1: Configure OSS permissions

**Grant required permissions to a managed Kubernetes cluster**

If your applications run in a managed Kubernetes cluster, you must grant required permissions to the cluster before you can use it to access an OSS bucket.

1.  Create a custom permission policy. For more information, see [Create a custom policy](/intl.en-US/Policy Management/Custom policies/Create a custom policy.md).

    **Note:** For information about how to regulate finer-grained access to an OSS bucket, see [Use RAM to manage OSS permissions](/intl.en-US/Tutorials/Use RAM to manage OSS permissions.md).

    To grant full permissions to the cluster, create a permission policy based on the following content:

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

    To grant only read and write permissions to the cluster, create a permission policy based on the following content:

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

2.  Grant permissions to the Resource Access Management \(RAM\) role of the manage Kubernetes cluster.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  On the details page of the cluster, click the **Cluster Resources** tab and view the worker role of the cluster.

    5.  Log on to the [RAM console](https://ram.console.aliyun.com/).

    6.  In the left-side navigation pane, click **Grants**.

    7.  On the Grants page, click **Grant Permission**. In the **Add Permissions** panel, set the parameters and click **OK**.

        |Parameter|Description|
        |---------|-----------|
        |Authorization|Valid values: **Alibaba Cloud account all resources** and **Specified Resource Group**.|
        |Principal|Enter the worker role of the cluster.|
        |Select Policy|Click **Custom Policy**, enter the name of the permission policy that is created in Step [1](#step_csd_xek_3x4), and then click the name of the policy.|


**Grant permissions to a registered cluster**

If your applications run in a registered cluster, you must create a RAM user for the cluster. Then, create an AccessKey pair for the RAM user and grant the RAM user permissions to access other cloud resources.

1.  Create a RAM user. For more information, see [Create a RAM user](/intl.en-US/RAM User Management/Create a RAM user.md).

2.  Create a custom permission policy to access OSS. For more information, see Step [1](#step_csd_xek_3x4).

3.  Grant permissions to the RAM user. For more information, see [Grant permissions to a RAM user](/intl.en-US/RAM User Management/Grant permissions to a RAM user.md).

4.  Create an AccessKey pair for the RAM user. For more information, see [Obtain an AccessKey pair]().

5.  Create a Secret in the registered cluster.

    To ensure that the AccessKey information is used only within the registered cluster, you must first use the AccessKey pair to deploy a Secret named alibaba-addon-secret in the cluster. This reduces the risk of information leakage.

    Similar to open source Velero, ACK also installs migrate-controller in the velero namespace. If the namespace does not exist, you must first create a namespace and name it velero. Then, use the AccessKey pair to create the alibaba-addon-secret Secret in the namespace.

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

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Application Backup \(Public Preview\)**.

5.  On the Application Backup page, click **Install**.

    **Note:** If the velero namespace does not exist, the system automatically creates the namespace when it installs the component. Do not delete this namespace when you back up applications.

    If the page in the following figure is displayed, it indicates that the component is installed.

    ![Application backup component](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2337284161/p214169.png)


## Step 3: Create a backup vault

When you back up applications in an ACK cluster, related data is stored in an OSS bucket. The following content describes how to access an OSS bucket:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

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
    |Network Type|Valid values: **Public Network** and **Internal Network**. If you can transmit data to the internal endpoint of the OSS bucket, select the internal network type. By default, ACK transmits data over the Internet.|


## Step 4: Create a backup task

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Application Backup \(Public Preview\)**.

5.  On the Application Backup page, click the **Backups** tab.

6.  On the **Backups** tab, click **Create**.

7.  If you back up a volume, run the following command to add an annotation to the pod to which the volume is mounted.

    **Note:** If you back up an application, skip this step.

    ```
    kubectl -n The name of the volume that you want to back up. annotate pod/The name of the pod. backup.velero.io/backup-volumes= The name of the persistent volume claim (PVC) that corresponds to the pod.
    ```

8.  In the **Create** panel, set the following parameters: **Name**, **Backup Vaults**, **Backup Namespace**, and **Validity Period**. After you set these parameters, click **OK**.

    **Note:** The name can contain only lowercase letters and digits.

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
    -   Existing resources are not overwritten during the restoration process. Only resources that do not exist in the current cluster are restored. If you want to restore an application to an earlier version, you must first delete the current resources of the application.
8.  In the message that appears, click **OK**.


## Restore an application to a specified cluster

You must create two clusters before you can restore an application to a specified cluster. In this example, the two clusters are cluster\_A and cluster\_B. The following example shows how to back up an application in cluster\_A and restore the application in cluster\_B:

**Note:** Make sure that the versions of the two Kubernetes clusters are the same. Otherwise, you may fail to restore the application to the specified cluster.

![Architecture](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1135242161/p223924.png)

1.  Grant cluster\_A and cluster\_B the required permissions on OSS. For more information, see [Step 1: Configure OSS permissions](#section_eex_psy_s4t).

2.  Install the application backup component in cluster\_A and cluster\_B. For more information, see [Step 2: Install the application backup component](#section_7v1_q3t_hak).

3.  Create a backup vault for both cluster\_A and cluster\_B. When you create backup vaults for cluster\_A and cluster\_B, select the same OSS bucket and set Network Type to Public Network. For more information, see [Step 3: Create a backup vault](#section_jcd_lxp_dpg).

4.  Create a backup task in cluster\_A. For more information, see [Step 4: Create a backup task](#section_v4n_i9q_da6).

    After the backup task is created, you can log on to cluster\_B to view the backup task on the **Backups** tab of the Application Backup page.

5.  On the Application Backup page, click the **Restore** tab, select the backup that is created in cluster\_A, and restore the backup. For more information, see [Step 5: Create a restoration task](#section_gdu_9n6_lft).


**Related topics**  


[Use migrate-controller to back up and restore applications](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Manage components/Use migrate-controller to back up and restore applications.md)

