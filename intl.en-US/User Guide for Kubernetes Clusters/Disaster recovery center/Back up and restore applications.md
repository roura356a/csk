---
keyword: [back up an application, restore an application]
---

# Back up and restore applications

Container Service for Kubernetes \(ACK\) allows you to back up and restore stateful applications deployed in an ACK cluster. This is an all-in-one solution to achieve crash consistency, application consistency, and cross-region disaster recovery for stateful applications in ACK clusters. This topic describes how to back up and restore applications and volumes by using the application backup feature or Velero.

The application backup component is installed. For more information, see [Install the application backup component](/intl.en-US/User Guide for Kubernetes Clusters/Disaster recovery center/Install the application backup component.md).

## Limits

-   You can create only full backups for applications. Incremental backups are not supported.
-   When you back up an application, the resources in the Deleting state are not backed up.
-   If you want to create a snapshot of a disk volume, you must install the Container Storage Interface \(CSI\) plug-in of V1.1.0 or later. For more information about how to install the CSI plug-in, see [Install and upgrade the CSI plug-in](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Install and upgrade the CSI plug-in.md).

## Method 1: Back up and restore applications by using the application backup feature in the console

**Step 1: Create a backup vault**

When you back up applications in an ACK cluster, the backup files are stored in an Object Storage Service \(OSS\) bucket.

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
    |Network Type|Valid values: **Public Network** and **Internal Network**. If your network allows you use the internal endpoint of the OSS bucket to transmit data, select Internal Network. By default, ACK transmits data over the internal network.|


**Step 2: Create a backup task**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Application Backup \(Public Preview\)**.

5.  On the Application Backup page, click the **Backups** tab.

6.  On the **Backups** tab, click **Create**.

7.  In the **Create** panel, set the parameters and click **OK**.

    ![Create a backup task](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0795159161/p245376.png)

    |Parameter|Description|
    |---------|-----------|
    |**Name**|The name of the backup task. This parameter is required.|
    |**Backup Vaults**|Select the backup vault that you want to use. This parameter is required.|
    |**Backup Namespaces**|You can select one or more namespaces that you want to back up. Applications in the selected namespaces are backed up.|
    |**Label**|Specify a label. Applications with the specified label are backed up.|
    |**Excluded Namespaces**|You can select one or more namespaces that you want to exclude from the backup task. Applications in the excluded namespaces are not backed up.|
    |**Excluded Resources**|You can specify one or more Kubernetes resources that you want to exclude from the backup task and separate them with commas \(,\), for example, `pod or Secret`. The excluded resources are not backed up.|
    |**Volume Backup**|Specify whether to enable volume backup. You can enable this feature to back up the application data stored in the relevant volume. If you select Enable, you must set **Backup Method**:

    -   **Disk Snapshot**: uses the disk snapshot feature of Alibaba Cloud disks to back up application data. Snapshots are stored in a disk.
    -   **Restic**: uses the open source Restic program to back up application data. Backup files are stored in OSS. |
    |**Validity Period**|Specify the retention period of backup files. Backup files cannot be restored after they expire.|
    |**Scheduled Backup**|Specify whether to periodically back up applications and application data. You can enter a cron expression to set a backup schedule. For more information about cron expressions, see [how-use-cron-linux](https://opensource.com/article/17/11/how-use-cron-linux).|

    **Note:**

    -   The name of the backup task can contain only lowercase letters and digits, and cannot contain space characters.
    -   You can select or exclude multiple namespaces.
    -   You can specify only one resource label in each backup task.
    -   If you select the Disk Snapshot option, the disk snapshot feature of Alibaba Cloud disks is used to back up a disk volume. If you select the Restic option, the open source Restic program is used to back up a disk volume. We recommend that you select the Disk Snapshot option.
    -   You can set a backup schedule by entering a cron expression or a backup interval.
    -   After you enable scheduled backup, you can view the details about scheduled backups on the **Scheduled Backup Configurations** tab of the Application Backup page.
    On the **Backups** tab, if the state of the backup task is **Completed**, the task is created.


**Step 3: Create a restoration task**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Application Backup \(Public Preview\)**.

5.  On the Application Backup page, click the **Restore** tab.

6.  On the **Restore** tab, click **Create Restoration Task**.

7.  In the **Create Restoration Task** panel, enter a name in the **Name** field, select a backup file from the **Backup Name** drop-down list, and then click **OK**.

    **Note:**

    -   The task name can contain only lowercase letters and digits.
    -   Existing resources in the cluster are not overwritten during the restoration process. Only resources that do not exist in the cluster are restored. Before you can restore an application to an earlier version, you must delete the existing resources of the application.
8.  In the message that appears, click **OK**.


## Method 2: Back up and restore applications by using Velero

Velero is a cloud native tool that is used to back up, restore, and migrate applications in Kubernetes clusters. Velero is written in Go. Velero ensures data security when you use it to back up, restore, and migrate applications and persistent volumes \(PVs\) in ACK clusters. For more information about the source code of the Velero project, see [velero](https://github.com/vmware-tanzu/velero). For more information about the source code of the Velero plug-in for Alibaba Cloud, see [velero-plugin](https://github.com/AliyunContainerService/velero-plugin).

Before you can use Velero, you must install the Velero client based on the operating system of your server. For more information, see [the Velero client](https://github.com/vmware-tanzu/velero/releases).

**Step 1: Configure an OSS bucket**

You must create a BackupStorageLocation custom resource definition \(CRD\) to specify the storage location for Velero backups.

1.  Use the following template to create a file named BackupStorageLocation.yaml:

    ```
    apiVersion: velero.io/v1
    kind: BackupStorageLocation
    metadata:
      labels:
        component: velero
      name: default
      namespace: velero
    spec:
      config:
        region: cn-hangzhou
      objectStorage:
        bucket: velero-bucket
      provider: alibabacloud
    ```

    **Note:**

    -   Set `spec.config.region` to the region where the OSS bucket is created.
    -   Set `spec.objectStorage.bucket` to the name of the OSS bucket.
    -   If your network allows you to transmit data to the internal OSS endpoint, add `spec.config.network: internal` to the file. This way, ACK transmits data over the internal network by default.
    -   To use a subdirectory of the OSS bucket, add `spe.objectStorage.prefix: <your prefix>`. By default, ACK uses the root directory of the OSS bucket.
2.  Run the following command to configure the OSS bucket:

    ```
    kubectl apply -f BackupStorageLocation.yaml
    ```


**Step 2: Back up applications**

Run the following command to use the Velero tool of migrate-controller and back up all resources in one or more specified namespaces of an ACK cluster.

```
velero backup create <BACKUP NAME> --include-namespaces <INCLUDE NAMESPACES>
```

The following examples show how to back up all resources in one namespace and all resources in two namespaces:

-   Run the following command to back up applications in the **nginx-examples** namespace:

    ```
    velero backup create backup-nginx --include-namespaces nginx-examples
    ```

-   Run the following command to back up applications in the **nginx-examples** and **wordpress** namespaces:

    ```
    velero backup create backup-nginx-and-wordpress --include-namespaces nginx-examples,wordpress
    ```


**Note:** The preceding operations do not back up the data in the PVs that are mounted to the applications. Only orchestration files in the JSON format are backed up.

If you want to back up the data in the PVs, perform the following steps:

1.  Run the following command to add the backup.velero.io/backup-volumes annotation to the pods to which the PVs are mounted.

    ```
    kubectl -n <NAMESPACE> annotate pod/<POD NAME> backup.velero.io/backup-volumes=<POD VOLUME NAME>
    ```

2.  After you add the annotation, run the following command to create a backup:

    ```
    velero backup create <BACKUP NAME> --include-namespaces <INCLUDE NAMESPACES>
    ```

    For example, you want to back up an NGINX application in the **nginx-examples** namespace and the PV of the application. The PV is named **nginx-logs** and mounted to the `nginx-deployment-7477779c4f-rz95l` pod. To back up the PV, perform the following steps:

    1.  Run the following command to add an annotation to the PV that is mounted to the pod:

        ```
        kubectl -n nginx-example annotate pod/nginx-deployment-7477779c4f-rz95l backup.velero.io/backup-volumes=nginx-logs
        ```

    2.  Run the following command to create a backup:

        ```
        velero backup create nginx-backup-with-pv --include-namespaces nginx-example
        ```


To back up applications at a scheduled time, run the following command to create a scheduled backup task:

```
velero schedule create <NAME> --include-namespaces <NAMESPACE> --schedule "SCHEDULE"
```

The following examples show how to create scheduled backup tasks that back up applications every seven days and every 24 hours:

-   Create a scheduled backup task named `nginx-app-with-pv-schedule-01` to back up applications in the **nginx-examples** namespace every seven days:

    ```
    velero schedule create nginx-app-with-pv-schedule-01 --include-namespaces nginx-examples --schedule "0 7 * * *"
    ```

-   Create a scheduled backup task named `nginx-app-with-pv-schedule-02` to back up applications in the **nginx-examples** namespace every 24 hours:

    ```
    velero schedule create nginx-app-with-pv-schedule-02 --include-namespaces nginx-examples --schedule "@every 24h"
    ```


**Step 3: Restore applications**

This example describes how to restore applications after the namespace to which the applications belong is deleted.

1.  Run the following command to delete the **nginx-example** namespace:

    ```
    kubectl delete ns nginx-example
    ```

2.  Run the following command to restore the applications:

    ```
    velero restore create --from-backup nginx-backup-without-pv
    ```


