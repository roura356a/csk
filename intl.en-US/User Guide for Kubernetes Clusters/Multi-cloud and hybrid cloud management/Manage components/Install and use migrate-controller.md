---
keyword: [migrate-controller, install and use]
---

# Install and use migrate-controller

The migrate-controller component is developed by Alibaba Cloud based on the open source project Velero. You can use this component to migrate Kubernetes applications. This topic describes how to install and use migrate-controller.

Velero is a cloud native tool that can be used to back up, restore, and migrate cluster applications. Velero is written in Golang. This tool ensures data security when you use it to back up, restore, and migrate applications and persistent volumes \(PVs\) in ACK clusters. For more information about source code of the Velero project, see [velero](https://github.com/vmware-tanzu/velero). For more information about the source code of the Velero plug-in for Alibaba Cloud, see [velero-plugin](https://github.com/AliyunContainerService/velero-plugin).

**Note:** Velero requires Kubernetes version later than V1.7.

## Step 1: Set permissions

Before you can install the component in a register cluster, you must create the AccessKey information to authenticate your requests and enable access to cloud resources. Before you create the AccessKey information, you must create a Resource Access Management \(RAM\) user and grant the RAM user permissions to access cloud resources.

1.  Create a RAM user.

    For more information, see [Create a RAM user](/intl.en-US/RAM User Management/Create a RAM user.md).

2.  Create a permission policy.

    For more information, see [Create a custom policy](/intl.en-US/Policy Management/Custom policies/Create a custom policy.md).

    The following code block shows the content of the permission policy for Velero:

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

3.  Grant permissions to the RAM user

    For more information, see [Grant permissions to a RAM user](/intl.en-US/RAM User Management/Grant permissions to a RAM user.md).

4.  Create the AccessKey information for the RAM user.

    For more information, see [Obtain an AccessKey pair]().


Run the following command to create the Secret that is required to use Velero:

```
kubectl create ns velero
```

```
kubectl -n velero create secret generic alibaba-addon-secret --from-literal='access-key-id=<your access key id>' --from-literal='access-key-secret=<your access key secret>'
```

**Note:**

Replace `<your access key id>` and `<your access key secret>` in the preceding command with the AccessKey information that you have obtained.

## Step 2: Install migrate-controller

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  On the Clusters page, find the cluster that you want to manage and select **Manage System Components** from the **More** drop-down list in the **Actions** column.

3.  On the Add-ons page, find the **migrate-controller** component and click **Install** for the component.


## Step 3: Install Velero

1.  [Download](https://github.com/vmware-tanzu/velero/releases) the client tool provided by Velero.

2.  Run the following command to decompress the downloaded .tar.gz file:

    ```
    tar -xvf <RELEASE-TARBALL-NAME>.tar.gz -C /dir/to/extract/to 
    ```

3.  Move the Velero binary file to an executable path.


## Step 4: Create the BackupStorageLocation file

BackupStorageLocation is used to specify the storage location for Velero backups.

1.  Create a file named BackupStorageLocation.yaml and copy the following content to the file:

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

    -   Set `spec.config.region` to the region where the Object Storage Service \(OSS\) bucket is located.
    -   Set `spec.objectStorage.bucket` to the OSS bucket name.
    -   If data is transmitted to an internal OSS endpoint, add the `spec.config.network:internal` parameter to the file. By default, Container Service for Kubernetes \(ACK\) enables transmission over the Internet.
    -   To use a specific subdirectory of the OSS bucket, add the `spe.objectStorage.prefix: <your prefix>` parameter to the file. By default, Container Service for Kubernetes \(ACK\) uses the root directory of the OSS bucket.
2.  Run the following command to create the BackupStorageLocation file:

    ```
    kubectl apply -f BackupStorageLocation.yaml
    ```


## Step 5: Back up an application

1.  Deploy the application nginx-app-with-pv.yaml.

    1.  Create a file named nginx-app-with-pv.yaml.

    2.  Use the following template to deploy the file:

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

2.  Run the following command to create the application:

    ```
    kubectl apply -f nginx-app-with-pv.yam
    ```

3.  Run the following command to view the application information:

    ```
    kubectl -n nginx-example get deployment
    ```

    ```
    NAME                                     READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.extensions/nginx-deployment   1/1     1            1           108s
    ```

    ```
    kubectl -n nginx-example get pod
    ```

    ```
    NAME                                    READY   STATUS    RESTARTS   AGE
    pod/nginx-deployment-7477779c4f-rz95l   2/2     Running   0          108s
    ```

    ```
    kubectl -n nginx-example get pvc
    ```

    ```
    NAME                               STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS        AGE
    persistentvolumeclaim/nginx-logs   Bound    d-bp1eutshfe774exa5os2   20Gi       RWO            alicloud-disk-ssd   108s
    ```

4.  Back up the namespace to which the application belongs.

    -   Run the following command to skip backup of persistent volume \(PV\) data:

        ```
        velero backup create nginx-backup-without-pv --include-namespaces nginx-example
        ```

    -   Run the following command to back up PV data:

        ```
        kubectl -n nginx-example annotate pod/nginx-deployment-7477779c4f-rz95l backup.velero.io/backup-volumes=nginx-logs
        ```

5.  Run the following command to create a backup:

    ```
    velero backup create nginx-backup-without-pv --include-namespaces nginx-example
    ```

6.  Run the following command to view the backup:

    ```
    velero backup get
    ```

    ```
    NAME                      STATUS      ERRORS   WARNINGS   CREATED                         EXPIRES   STORAGE LOCATION   SELECTOR
    nginx-backup-without-pv   Completed   0        0          2020-09-18 14:43:13 +0800 CST   29d       default            <none>
    ```


## Step 6: Create scheduled backup tasks

To back up the application at a scheduled time, you can enable scheduled backup. Perform the following steps:

1.  Add an annotation to the PV that you want to back up.

    ```
    kubectl -n nginx-example annotate pod/nginx-deployment-7477779c4f-rz95l backup.velero.io/backup-volumes=nginx-logs
    ```

2.  Create a scheduled backup task.

    For example, you can create a scheduled task to back up the application every seven days.

    ```
    velero schedule create nginx-app-with-pv-schedule --schedule "0 7 * * *"
    ```


## Step 7: Restore the application

The following example shows how to restore an application after a namespace is deleted due to user errors.

1.  Run the following command to delete the nginx-example namespace:

    ```
    kubectl delete ns nginx-example
    ```

2.  Run the following command to restore the application:

    ```
    velero restore create --from-backup nginx-backup-without-pv
    ```


-   [Prerequisites](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Manage components/Prerequisites.md)
-   [Release notes for migrate-controller](/intl.en-US/Release notes/System Component change Records/migrate-controller.md)

  


