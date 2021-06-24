---
keyword: [cross cluster, migration, backup]
---

# Migrate applications across clusters

Container Service for Kubernetes \(ACK\) allows you to back up and restore stateful applications deployed in an ACK cluster. This is an all-in-one solution to achieve crash consistency, application consistency, and cross-region disaster recovery for stateful applications in ACK clusters. This topic describes how to migrate applications across clusters by using the application backup feature or Velero.

Two Kubernetes clusters of the same version are created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

## Introduction

In this example, a cluster named Cluster\_A and a cluster named Cluster\_B are used. The following example shows how to back up an application in Cluster\_A and restore the application to Cluster\_B.

**Note:** Make sure that the two clusters run the same Kubernetes version. Otherwise, you cannot migrate the application to the specified cluster.

![Architecture](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1135242161/p223924.png)

## Method 1: Migrate an application by using the application backup feature

1.  Grant the required Object Storage Service \(OSS\) permissions to Cluster\_A and Cluster\_B. For more information, see [Configure OSS permissions](/intl.en-US/User Guide for Kubernetes Clusters/Disaster recovery center/Install the application backup component.md).

2.  Install the application backup component in Cluster\_A and Cluster\_B. For more information, see [Install the application backup component](/intl.en-US/User Guide for Kubernetes Clusters/Disaster recovery center/Install the application backup component.md).

3.  Create backup vaults for Cluster\_A and Cluster\_B. When you create backup vaults for Cluster\_A and Cluster\_B, select the same OSS bucket and set Network Type to Public Network. For more information, see [Create a backup vault](/intl.en-US/User Guide for Kubernetes Clusters/Disaster recovery center/Back up and restore applications.md).

4.  Create a backup task in Cluster\_A. For more information, see [Create a backup task](/intl.en-US/User Guide for Kubernetes Clusters/Disaster recovery center/Back up and restore applications.md).

    After you create the backup task in Cluster\_A, you can log on to Cluster\_B to view the backup task on the **Backups** tab of the Application Backup page.

5.  On the Application Backup page, click the **Restore** tab and create a restoration task based on the backup task that is created in Cluster\_A. For more information, see [Create a restoration task](/intl.en-US/User Guide for Kubernetes Clusters/Disaster recovery center/Back up and restore applications.md).


## Method 2: Migrate an application by using Velero

This example shows how to migrate an application in the nginx-examples namespace from Cluster\_A to Cluster\_B. To migrate the application, perform the following steps:

1.  Install the Velero client and the application backup component in Cluster\_A. Then, configure and connect to the OSS bucket named mybackups. For more information, see [velero](https://github.com/vmware-tanzu/velero/releases) and [Install the application backup component](/intl.en-US/User Guide for Kubernetes Clusters/Disaster recovery center/Install the application backup component.md).

2.  Install the Velero client and migrate-controller in Cluster\_B. Then, configure and connect to the OSS bucket named **mybackups**.

3.  Back up applications in the **nginx-examples** namespace in Cluster\_A and specify the name of the backup file, for example, **nginx-backup**.

4.  Use the backup file named **nginx-backup** in Cluster\_B to restore the applications. This operation migrates the applications from Cluster\_A to Cluster\_B.


In this example, an application in Cluster\_A is backed up and then restored to Cluster\_B. This way, the application is migrated from Cluster\_A to Cluster\_B. Before you back up and restore the application, you must complete the operations in [Step 1](#step_4j2_1xc_1r1) and [Step 2](#step_sfx_2d1_wdt). The following example shows how to back up and restore the application:

1.  Deploy the sample application named nginx-app-with-pv in Cluster\_A.

    1.  Create a file named nginx-app-with-pv.yaml and copy the following content to the file:

        ```
        ---
        apiVersion: v1
        kind: Namespace
        metadata:
          name: nginx-example
          labels:
            app: nginx
        
        ---
        apiVersion: v1
        kind: PersistentVolumeClaim
        metadata:
          name: nginx-logs
          namespace: nginx-example
        spec:
          accessModes:
          - ReadWriteOnce
          resources:
            requests:
              storage: 20Gi
          storageClassName: alicloud-disk-ssd
        
        ---
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: nginx-deployment
          namespace: nginx-example
        spec:
          replicas: 1
          template:
            metadata:
              labels:
                app: nginx
              annotations:
                pre.hook.backup.velero.io/container: fsfreeze
                pre.hook.backup.velero.io/command: '["/sbin/fsfreeze", "--freeze", "/var/log/nginx"]'
                post.hook.backup.velero.io/container: fsfreeze
                post.hook.backup.velero.io/command: '["/sbin/fsfreeze", "--unfreeze", "/var/log/nginx"]'
            spec:
              volumes:
                - name: nginx-logs
                  persistentVolumeClaim:
                   claimName: nginx-logs
              containers:
              - image: nginx:1.7.9
                name: nginx
                ports:
                - containerPort: 80
                volumeMounts:
                  - mountPath: "/var/log/nginx"
                    name: nginx-logs
                    readOnly: false
              # sync from gcr.io/heptio-images/fsfreeze-pause:latest
              - image: registry.cn-hangzhou.aliyuncs.com/acs/fsfreeze-pause:latest
                name: fsfreeze
                securityContext:
                  privileged: true
                volumeMounts:
                  - mountPath: "/var/log/nginx"
                    name: nginx-logs
                    readOnly: false
        ```

    2.  Run the following command to deploy the application to Cluster\_A:

        ```
        kubectl apply -f nginx-app-with-pv.yam
        ```

2.  Run the following commands to view information about the application.

    -   Run the following command to query the Deployment:

        ```
        kubectl -n nginx-example get deployment
        ```

        Expected output:

        ```
        NAME                                     READY   UP-TO-DATE   AVAILABLE   AGE
        deployment.extensions/nginx-deployment   1/1     1            1           108s
        ```

    -   Run the following command to view the pod:

        ```
        kubectl -n nginx-example get pod
        ```

        Expected output:

        ```
        NAME                                    READY   STATUS    RESTARTS   AGE
        pod/nginx-deployment-7477779c4f-rz95l   2/2     Running   0          108s
        ```

    -   Run the following command to query information about the PVC:

        ```
        kubectl -n nginx-example get pvc
        ```

        Expected output:

        ```
        NAME                               STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS        AGE
        persistentvolumeclaim/nginx-logs   Bound    d-bp1eutshfe774exa5os2   20Gi       RWO            alicloud-disk-ssd   108s
        ```

3.  Back up the namespace to which the application belongs.

    -   Run the following command to back up the namespace to which the application belongs \(excluding PV data\):

        ```
        velero backup create nginx-backup-without-pv --include-namespaces nginx-example
        ```

    -   Run the following command to back up the namespace to which the application belongs \(including PV data\).
        1.  Run the following command to add annotations to the pod volume that needs to be backed up:

            ```
            kubectl -n nginx-example annotate pod/nginx-deployment-7477779c4f-rz95l backup.velero.io/backup-volumes=nginx-logs
            ```

        2.  Run the following command to back up the application:

            ```
            velero backup create nginx-backup-with-pv --include-namespaces nginx-example
            ```

4.  Run the following command to view information about the backup file:

    ```
    velero backup get
    ```

    Expected output \(excluding PV data\):

    ```
    NAME                      STATUS      ERRORS   WARNINGS   CREATED                         EXPIRES   STORAGE LOCATION   SELECTOR
    nginx-backup-without-pv   Completed   0        0          2020-09-18 14:43:13 +0800 CST   29d       default            <none>
    ```

    The output indicates that a backup file is created for the application.

5.  Run the following command to restore the application to Cluster\_B \(excluding PV data\):

    ```
    velero restore create --from-backup nginx-backup-without-pv
    ```


