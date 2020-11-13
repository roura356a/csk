# Migrate applications to an ACK cluster

This topic describes how to use Velero and restic to migrate cloud-native applications and data on persistent volumes \(PVs\) from a user-created Kubernetes cluster to an Alibaba Cloud Container Service for Kubernetes \(ACK\) cluster. You can also use Velero and restic to migrate applications and data on PVs from a Kubernetes cluster managed by another cloud provider to an ACK cluster.

In most cases, a user-created Kubernetes cluster resides in a data center and container images are stored in a user-created image repository. Before you migrate applications, you must migrate the container images to Alibaba Cloud Container Registry \(ACR\). For more information, see [Migrate container images](/intl.en-US/Best Practices/Migrate a self-built Kubernetes cluster to Container Service for Kubernetes/Migrate container images/Use image-syncer to migrate container images.md).

In this topic, assume that the WordPress application uses the following container images:

```
registry.api.paas.com:5000/admin/wordpress:latest
registry.api.paas.com:5000/admin/mysql:8
```

The following block shows the addresses of the images after migration:

```
registry.cn-hangzhou.aliyuncs.com/ack-migration/wordpress:latest
registry.cn-hangzhou.aliyuncs.com/ack-migration/mysql:8
```

This topic describes how to migrate the WordPress application from a user-created Kubernetes cluster to an ACK cluster. The WordPress application consists of two components: WordPress and MySQL. Each component uses a Network File System \(NFS\) PV to store application data. The WordPress application provides services of the NodePort type.

## Procedure

1.  [Set up the environment](#section_sxm_2d9_ozp)
2.  [Back up the application in the user-created Kubernetes cluster](#section_u5c_g0l_kij)
3.  [Restore the application in the ACK cluster](#section_9yk_jhw_mti)
4.  [Update application configurations](#section_0n4_819_ijz)
5.  [Debug and start the application.](#section_wcx_fog_7hy)

## Set up the environment

Perform the following steps to deploy Velero in the ACK cluster and the user-created Kubernetes cluster.

**Note:** You must deploy both the Velero client and the Velero server.

1.  Install the Velero client.

    Download [Velero](https://github.com/vmware-tanzu/velero/releases). Run the following command to install Velero and verify the result.

    ```
    curl -o /usr/bin/velero https://public-bucket-1.oss-cn-hangzhou.aliyuncs.com/velero && chmod  x /usr/bin/velero 
    ```

2.  Create an OSS bucket. For more information, see [Create buckets](/intl.en-US/Quick Start/Create buckets.md).

    You must first create an OSS bucket to store the application and PV data. We recommend that you create an OSS bucket for each user-created Kubernetes cluster.

    1.  Log on to the [OSS console](https://oss.console.aliyun.com/).

    2.  On the **Overview** page, click **Create Bucket** on the right side. You can also click the icon next to Storage Capacity on the left side.

    3.  In the Create Bucket panel, set parameters for the bucket.

        In this example, the OSS bucket is named `ls-velero` and deployed in the China \(Hangzhou\) region.

3.  Create a Resource Access Management \(RAM\) user and generate an AccessKey pair. For more information, see [Create a RAM user](/intl.en-US/RAM User Management/Create a RAM user.md).

    If you use the AccessKey pair of an Alibaba Cloud account, skip this step.

    ```
    {
        "Version": "1",
        "Statement": [
            {
                "Action": [
                    "ecs:DescribeSnapshots",
                    "ecs:CreateSnapshot",
                    "ecs:DeleteSnapshot",
                    "ecs:DescribeDisks",
                    "ecs:CreateDisk",
                    "ecs:Addtags",
                    "oss:PutObject",
                    "oss:GetObject",
                    "oss:DeleteObject",
                    "oss:GetBucket",
                    "oss:ListObjects"
                ],
                "Resource": [
                    "*"
                ],
                "Effect": "Allow"
            }
        ]
    }
    ```

4.  Deploy the Velero server.

    1.  Add the AccessKey pair generated in [Step 3](#step_1et_49b_ow4) to the credentials-velero deployment file of Velero.

        ```
        ALIBABA_CLOUD_ACCESS_KEY_ID=<access_key_id>
        ALIBABA_CLOUD_ACCESS_KEY_SECRET=<access_key_secret>
        ```

    2.  Run the following command to deploy Velero:

        ```
        velero install --provider alibabacloud --image registry.cn-hangzhou.aliyuncs.com/haoshuwei24/velero:v1.2.0 --bucket ls-velero --secret-file ./credentials-velero --use-volume-snapshots=false --backup-location-config region=cn-hangzhou --use-restic --plugins registry.cn-hangzhou.aliyuncs.com/acs/velero-plugin-alibabacloud:v1.2 --wait
        ```

    Run the following command to query the pod status:

    ```
    kubectl -n velero get po
    ```

    ```
    NAME                      READY   STATUS    RESTARTS   AGE
    restic-fqwsc              1/1     Running   0          41s
    restic-kfzqt              1/1     Running   0          41s
    restic-klxhc              1/1     Running   0          41s
    restic-ql2kr              1/1     Running   0          41s
    restic-qrsrn              1/1     Running   0          41s
    restic-srjmm              1/1     Running   0          41s
    velero-67b975f5cb-68nj4   1/1     Running   0          41s
    ```


## Back up the application in the user-created Kubernetes cluster

-   If you want to back up only the WordPress application, run the following commands:

    ```
    velero backup create wordpress-backup-without-pv --include-namespaces wordpress
    ```

    ```
    # Output
    Backup request "wordpress-backup-without-pv" submitted successfully.
    Run `velero backup describe wordpress-backup-without-pv` or `velero backup logs wordpress-backup-without-pv` for more details.
    ```

    ```
    velero backup get
    ```

    ```
    # Output
    NAME                          STATUS      CREATED                         EXPIRES   STORAGE LOCATION   SELECTOR
    wordpress-backup-without-pv   Completed   2019-12-12 14:08:24  0800 CST   29d       default            <none>
    ```

-   If you want to back up the WordPress application and PV data, run the following commands:
    1.  ```
# Annotate the pods to which PVs are attached. Assume that the WordPress application runs on the wordpress-7cf5849f47-mbvx4 and mysql-74dddbdcc8-h2tls pods.
kubectl -n wordpress annotate pod/wordpress-7cf5849f47-mbvx4 backup.velero.io/backup-volumes=wordpress-persistent-storage
pod/wordpress-7cf5849f47-mbvx4 annotated
```

    2.  ```
# The PV attached to the wordpress-7cf5849f47-mbvx4 pod is mysql-persistent-storage. The PV attached to the mysql-74dddbdcc8-h2tls pod is wordpress-persistent-storage. Run the following commands to annotate the pods:
kubectl -n wordpress annotate pod/mysql-74dddbdcc8-h2tls backup.velero.io/backup-volumes=mysql-persistent-storage
pod/mysql-74dddbdcc8-h2tls annotated
```

    3.  ```
# Back up the WordPress application.
velero backup create wordpress-backup-with-pv --include-namespaces wordpress
```

        ```
        # Output
        Backup request "wordpress-backup-with-pv" submitted successfully.
        Run `velero backup describe wordpress-backup-with-pv` or `velero backup logs wordpress-backup-with-pv` for more details.
        ```

    4.  ```
velero backup get
```

        ```
        # Output
        NAME                          STATUS      CREATED                         EXPIRES   STORAGE LOCATION   SELECTOR
        wordpress-backup-with-pv      Completed   2019-12-12 14:23:40  0800 CST   29d       default            <none>
        wordpress-backup-without-pv   Completed   2019-12-12 14:08:24  0800 CST   29d       default            <none>
        ```


In the [OSS console](https://oss.console.aliyun.com/), you can view the files that have been backed up in the OSS bucket.

## Restore the application in the ACK cluster

In the user-created Kubernetes cluster, the WordPress application uses NFS PVs. In the ACK cluster, you can configure Apsara File Storage NAS volumes to store data of the WordPress application. In this example, a StorageClass named nfs is created for the WordPress application, and Alibaba Cloud SSDs are used as backend storage.

The CSI plug-in is used in this example. For more information, see [Provision Alibaba Cloud disks as dynamic volumes](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Disk volumes/Provision Alibaba Cloud disks as dynamic volumes.md).

1.  Create a StorageClass.

    If you back up only the WordPress application, skip this step.

    ```
    cat nfs.yaml
    ```

    ```
    # Output
    apiVersion: storage.k8s.io/v1
    kind: StorageClass
    metadata:
       name: nfs
    provisioner: diskplugin.csi.alibabacloud.com
    parameters:
        type: cloud_ssd
    reclaimPolicy: Retain
    ```

    ```
    kubectl apply -f nfs.yaml
    ```

    ```
    # Output
    storageclass.storage.k8s.io/nfs created
    ```

2.  Restore the WordPress application.

    Use Velero to restore the WordPress application in the ACK cluster. This completes the migration process.

    ```
    $ velero restore create --from-backup wordpress-backup-with-pv
    $ velero restore get
    NAME                                      BACKUP                     STATUS       WARNINGS   ERRORS   CREATED                         SELECTOR
    wordpress-backup-with-pv-20191212152745   wordpress-backup-with-pv   InProgress   0          0        2019-12-12 15:27:45  0800 CST   <none>
    $ velero restore get
    ```

    Run the following command to check the status of the WordPress application. An error is returned to indicate that the container images cannot be pulled.

    ```
    $ kubectl -n wordpress get po
    NAME                         READY   STATUS         RESTARTS   AGE
    mysql-669b4666cd-trsnz       0/1     ErrImagePull   0          19m
    mysql-74dddbdcc8-h2tls       0/1     Init:0/1       0          19m
    wordpress-7cf5849f47-mbvx4   0/1     Init:0/1       0          19m
    wordpress-bb5d74d95-xcjxw    0/1     ErrImagePull   0          19m
    ```


## Update application configurations

Application configurations include the image address, service exposure method, and the mount point of the storage disks used by the application. This topic describes only how to update the image address.

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, Choose **Applications** \> **Deployments**. Select the cluster and namespace to which the application is migrated.

3.  Find the WordPress application, and choose **More** \> **View in Yaml** in the **Actions** column.

4.  In the Edit YAML dialog box, change the `image` field to the address of the application image after migration and click **Update**.

    **Note:** You can obtain the address of the application image after migration from the Prerequisites section in this topic.

    Run the following command to check the status of the WordPress application:

    ```
    kubectl -n wordpress get po
    ```

    ```
    NAME                         READY   STATUS    RESTARTS   AGE
    mysql-678b5d8499-vckfd       1/1     Running   0          100s
    wordpress-8566f5f7d8-7shk6   1/1     Running   0          3m18s
    ```


## Debug and start the application.

The WordPress application consists of the WordPress and MySQL components. Each component uses an NFS PV to store application data. The WordPress application provides services of the NodePort type. You can use the following YAML file to debug and start the application:

```
# 1. Create a StorageClass named nfs.
cat nfs-sc.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: nfs
provisioner: helm.default/nfs
reclaimPolicy: Delete
kubectl apply -f  nfs-sc.yaml

# 2. Create a secret to store the password of the MySQL component. For example, if the password is mysql, run the echo -n "mysql" |base64 command to query the Base64-encoded string of the password.
cat secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysql
type: Opaque
data:
  password: bXlzcWw=
kubectl apply -f secret.yaml

# 3. Create a service, a persistent volume claim (PVC), and a deployment for the MySQL component.
cat mysql.yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql
  labels:
    app: mysql
spec:
  type: ClusterIP
  ports:
    - port: 3306
  selector:
    app: mysql
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-volumeclaim
  annotations:
    volume.beta.kubernetes.io/storage-class: "nfs"
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 20Gi

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql
  labels:
    app: mysql
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      securityContext:
        runAsUser: 999
        runAsGroup: 999
        fsGroup: 999
      containers:
        - image: registry.api.paas.com:5000/admin/mysql:8
          name: mysql
          args:
            - "--default-authentication-plugin=mysql_native_password"
          env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql
                  key: password
          ports:
            - containerPort: 3306
              name: mysql
          volumeMounts:
            - name: mysql-persistent-storage
              mountPath: /var/lib/mysql
      volumes:
        - name: mysql-persistent-storage
          persistentVolumeClaim:
            claimName: mysql-volumeclaim
kubectl apply -f mysql.yaml

 # 4. Create a PVC, a deployment, and a service for the WordPress component.
cat wordpress.yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: wordpress
  name: wordpress
spec:
  ports:
    - port: 80
      targetPort: 80
      protocol: TCP
      nodePort: 31570
  selector:
    app: wordpress
  type: NodePort
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: wordpress-volumeclaim
  annotations:
    volume.beta.kubernetes.io/storage-class: "nfs"
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 20Gi
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress
  labels:
    app: wordpress
spec:
  replicas: 1
  selector:
    matchLabels:
      app: wordpress
  template:
    metadata:
      labels:
        app: wordpress
    spec:
      containers:
        - image: registry.api.paas.com:5000/admin/wordpress
          name: wordpress
          env:
          - name: WORDPRESS_DB_HOST
            value: mysql:3306
          - name: WORDPRESS_DB_PASSWORD
            valueFrom:
              secretKeyRef:
                name: mysql
                key: password
          ports:
            - containerPort: 80
              name: wordpress
          volumeMounts:
            - name: wordpress-persistent-storage
              mountPath: /var/www/html
      volumes:
        - name: wordpress-persistent-storage
          persistentVolumeClaim:
            claimName: wordpress-volumeclaim
 kubectl apply -f wordpress.yaml
```

In the test environment, bind the domain name of the WordPress application to the IP address of the node where the application is deployed in the hosts file of your operating system. Then, access the WordPress application through http://wordpress.myk8s.paas.com:31570/.

