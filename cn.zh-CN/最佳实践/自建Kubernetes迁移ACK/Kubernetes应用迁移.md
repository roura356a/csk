---
keyword: [应用迁移, Velero Restic]
---

# Kubernetes应用迁移

本文记录使用Velero Restic快速完成云原生应用及PV数据从自建Kubernetes迁移到阿里云Kubernetes集群的实践过程。 此过程也同样适用于其他云厂商Kubernetes集群内的应用及PV数据迁移至阿里云Kubernetes集群。

自建Kubernetes集群通常位于用户自己的IDC中，容器镜像的存储也会使用自建镜像仓库，在自建Kubernetes应用迁移上云之前，您需要先将容器镜像迁移上云到ACR。具体步骤，请参见[容器镜像迁移](/cn.zh-CN/最佳实践/自建Kubernetes迁移ACK/容器镜像迁移/通过image-syncer工具迁移容器镜像.md)。

本示例中，假设wordpress应用中涉及如下容器镜像。

```
registry.api.paas.com:5000/admin/wordpress:latest
registry.api.paas.com:5000/admin/mysql:8
```

迁移上云后的镜像如下。

```
registry.cn-hangzhou.aliyuncs.com/ack-migration/wordpress:latest
registry.cn-hangzhou.aliyuncs.com/ack-migration/mysql:8
```

在本示例中， 我们将对自建Kubernetes集群中的一个Wordpress应用整体迁移上云到ACK，Wordpress示例应用分Wordpress和MySQL两个组件， 分别绑定两个不同的NFS Volume用于应用数据的持久化存储，最后通过NodePort暴露服务。

## 操作步骤

1.  [准备迁移环境](#section_sxm_2d9_ozp)
2.  [在自建Kubernetes集群备份应用](#section_u5c_g0l_kij)
3.  [在阿里云Kubernetes集群恢复应用](#section_9yk_jhw_mti)
4.  [更新应用配置](#section_0n4_819_ijz)
5.  [调试并启动应用](#section_wcx_fog_7hy)

## 准备迁移环境

请按照以下步骤，分别在阿里云Kubernetes集群和自建Kubernetes集群中部署Velero。

**说明：** 部署Velero包含部署Velero客户端和部署Velero服务器。

1.  安装Velero客户端。

    下载[Velero](https://github.com/vmware-tanzu/velero/releases)客户端工具，并执行以下命令安装和验证Velero客户端。

    ```
    curl -o /usr/bin/velero https://public-bucket-1.oss-cn-hangzhou.aliyuncs.com/velero && chmod +x /usr/bin/velero 
    ```

2.  创建OSS Bucket。请参见[创建存储空间](/cn.zh-CN/快速入门/控制台快速入门/创建存储空间.md)。

    velero 要求您需要先创建一个OSS Bucket，用于存储 Kubernetes 应用数据及其PV数据， 推荐每个Kubernetes集群单独使用各自的OSS Bucket。

    1.  登录 [OSS管理控制台](https://oss.console.aliyun.com/)。

    2.  您可以在**概览**页，单击右侧的**创建Bucket**。您也可以单击左侧存储空间后的+号。

    3.  在创建 Bucket 对话框配置Bucket参数。

        本示例中创建的OSS Bucket名称为`ls-velero`，创建的区域为华东1（杭州）。

3.  创建RAM账号并生成AccessKey。请参见[创建RAM用户](/cn.zh-CN/用户管理/创建RAM用户.md)。

    如果您使用主账号AccessKey，可以跳过此步骤。

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

4.  部署Velero服务端。

    1.  将[步骤三](#step_1et_49b_ow4)生成的AccessKey信息填入Velero的部署文件credentials-velero中。

        ```
        ALIBABA_CLOUD_ACCESS_KEY_ID=<access_key_id>
        ALIBABA_CLOUD_ACCESS_KEY_SECRET=<access_key_secret>
        ```

    2.  执行以下命令部署Velero。

        ```
        velero install --provider alibabacloud --image registry.cn-hangzhou.aliyuncs.com/haoshuwei24/velero:v1.2.0 --bucket ls-velero --secret-file ./credentials-velero --use-volume-snapshots=false --backup-location-config region=cn-hangzhou --use-restic --plugins registry.cn-hangzhou.aliyuncs.com/acs/velero-plugin-alibabacloud:v1.2 --wait
        ```

    执行以下命令，可以查看pod的运行状态。

    ```
    kubectl -n velero get po
    ```

    预期输出：

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


## 在自建Kubernetes集群备份应用

-   如果只需要备份Wordpress应用而不备份PV数据，执行以下操作。

    ```
    velero backup create wordpress-backup-without-pv --include-namespaces wordpress
    ```

    预期输出：

    ```
    Backup request "wordpress-backup-without-pv" submitted successfully.
    Run `velero backup describe wordpress-backup-without-pv` or `velero backup logs wordpress-backup-without-pv` for more details.
    ```

    执行以下命令备份应用。

    ```
    velero backup get
    ```

    预期输出：

    ```
    NAME                          STATUS      CREATED                         EXPIRES   STORAGE LOCATION   SELECTOR
    wordpress-backup-without-pv   Completed   2019-12-12 14:08:24 +0800 CST   29d       default            <none>
    ```

-   如果需要备份带PV数据的Wordpress应用，执行以下操作。
    1.  ```
# 首先需要为挂载pv数据卷的pod添加annotation， 例如wordpress应用运行了2个pod， 分别为wordpress-7cf5849f47-mbvx4 mysql-74dddbdcc8-h2tls， wordpress-7cf5849f47-mbvx4
kubectl -n wordpress annotate pod/wordpress-7cf5849f47-mbvx4 backup.velero.io/backup-volumes=wordpress-persistent-storage
pod/wordpress-7cf5849f47-mbvx4 annotated
```

    2.  ```
# 挂载的volume名为mysql-persistent-storage， mysql-74dddbdcc8-h2tls挂载的volume名为wordpress-persistent-storage, 则添加annotation的命令为
kubectl -n wordpress annotate pod/mysql-74dddbdcc8-h2tls backup.velero.io/backup-volumes=mysql-persistent-storage
pod/mysql-74dddbdcc8-h2tls annotated
```

    3.  ```
# 备份wordpress
velero backup create wordpress-backup-with-pv --include-namespaces wordpress
```

        预期输出：

        ```
        Backup request "wordpress-backup-with-pv" submitted successfully.
        Run `velero backup describe wordpress-backup-with-pv` or `velero backup logs wordpress-backup-with-pv` for more details.
        ```

    4.  ```
velero backup get
```

        预期输出：

        ```
        NAME                          STATUS      CREATED                         EXPIRES   STORAGE LOCATION   SELECTOR
        wordpress-backup-with-pv      Completed   2019-12-12 14:23:40 +0800 CST   29d       default            <none>
        wordpress-backup-without-pv   Completed   2019-12-12 14:08:24 +0800 CST   29d       default            <none>
        ```


完成后，在[OSS管理控制台](https://oss.console.aliyun.com/)，查看OSS Bucket可以看到备份的文件。

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8763659951/p73506.png)

## 在阿里云Kubernetes集群恢复应用

Wordpress应用使用NFS类型持久化数据卷，相应的在ACK中可以适配NAS Volume，在本示例中使用StorageClass NFS， 但后端存储介质使用SSD云盘块存储。

本示例使用阿里云Kubernetes集群所使用的CSI Plugin， 请参见[动态云盘卷](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/云盘存储卷/动态云盘卷.md)。

1.  创建StorageClass。

    如果在阿里云Kubernetes集群上备份Wordpress应用不带PV数据，则跳过此步骤。

    ```
    cat nfs.yaml
    ```

    示例模板如下所示：

    ```
    #输出
    apiVersion: storage.k8s.io/v1
    kind: StorageClass
    metadata:
       name: nfs
    provisioner: diskplugin.csi.alibabacloud.com
    parameters:
        type: cloud_ssd
    reclaimPolicy: Retain
    ```

    执行以下命令部署应用。

    ```
    kubectl apply -f nfs.yaml
    ```

    预期输出：

    ```
    storageclass.storage.k8s.io/nfs created
    ```

2.  恢复Wordpress应用。

    使用Velero恢复Wordpress应用到阿里云Kubernetes集群，完成Wordpress从自建Kubernetes集群到阿里云Kubernetes集群的迁移。

    ```
    velero restore create --from-backup wordpress-backup-with-pv
    velero restore get
    ```

    预期输出：

    ```
    NAME                                      BACKUP                     STATUS       WARNINGS   ERRORS   CREATED                         SELECTOR
    wordpress-backup-with-pv-20191212152745   wordpress-backup-with-pv   InProgress   0          0        2019-12-12 15:27:45 +0800 CST   <none>
    ```

    此时查看wordpress应用运行情况，会有镜像拉取失败的问题。

    ```
    $ kubectl -n wordpress get po
    ```

    预期输出：

    ```
    NAME                         READY   STATUS         RESTARTS   AGE
    mysql-669b4666cd-trsnz       0/1     ErrImagePull   0          19m
    mysql-74dddbdcc8-h2tls       0/1     Init:0/1       0          19m
    wordpress-7cf5849f47-mbvx4   0/1     Init:0/1       0          19m
    wordpress-bb5d74d95-xcjxw    0/1     ErrImagePull   0          19m
    ```


## 更新应用配置

应用配置项主要包含镜像地址、服务暴露方式及存储盘挂载等。本例中 ，仅涉及更新镜像地址。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在 Kubernetes 菜单下，单击左侧导航栏的**应用** \> **无状态**，选择目标集群和命名空间。

3.  在wordpress应用右侧**操作**列单击**更多** \> **查看Yaml**。

4.  在编辑 YAML页面把`image`字段替换成迁移后的镜像地址后，单击**更新**。

    **说明：** 您可以在前提条件中获取迁移后的镜像地址。

    查看Wordpress应用运行情况。

    ```
    kubectl -n wordpress get po
    ```

    预期输出：

    ```
    NAME                         READY   STATUS    RESTARTS   AGE
    mysql-678b5d8499-vckfd       1/1     Running   0          100s
    wordpress-8566f5f7d8-7shk6   1/1     Running   0          3m18s
    ```


## 调试并启动应用

Wordpress示例应用分Wordpress和MySQL两个组件， 分别绑定两个不同的NFS Volume用于应用数据的持久化存储，最后通过NodePort暴露服务。yaml文件示例如下：

```
# 1. 创建nfs storageclass
cat nfs-sc.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: nfs
provisioner: helm.default/nfs
reclaimPolicy: Delete
kubectl apply -f  nfs-sc.yaml

# 2. 创建mysql password的secret, echo -n "mysql" |base64
cat secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysql
type: Opaque
data:
  password: bXlzcWw=
kubectl apply -f secret.yaml

# 3. 创建mysql的pvc deployment service
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

 # 4. 创建wordpress的pvc deployment service
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

测试环境绑定Hosts后，通过http://wordpress.myk8s.paas.com:31570/访问Wordpress应用。

![wordpress](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8763659951/p94229.png)

