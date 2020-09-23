---
keyword: [Migrate Controller, 安装和使用]
---

# 安装和使用Migrate Controller

Migrate Controller是基于开源项目Velero开发的一个Kubernetes应用迁移的组件。本文介绍如何安装和使用Migrate Controller。

Velero是一个云原生的集群应用备份、恢复和迁移工具。Velero采用GO语言编写，可以安全地备份、恢复和迁移Kubernetes集群中的应用及其持久化存储卷。有关Velero源码项目地址，请参见[velero](https://github.com/vmware-tanzu/velero)。有关Velero Plugin for Alibaba Cloud源码项目地址，请参见[velero-plugin](https://github.com/AliyunContainerService/velero-plugin)。

**说明：** Velero的运行环境要求Kubernetes集群版本大于v1.7。

## 步骤一：设置权限

在注册集群中安装组件前，您需要在接入集群中设置AK用来访问云服务的权限。设置AK前，您需要创建RAM用户并为其添加访问相关云资源的权限。

1.  创建RAM用户。

    有关如何创建RAM用户的具体步骤，请参见[创建RAM用户](/cn.zh-CN/用户管理/创建RAM用户.md)。

2.  创建权限策略。

    有关创建权限策略的具体操作步骤，请参见[创建自定义策略](/cn.zh-CN/权限策略管理/自定义策略/创建自定义策略.md)。

    Velero组件权限策略信息如下。

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

3.  为RAM用户添加权限。

    有关如何为RAM用户授权的具体步骤，请参见[为RAM用户授权](/cn.zh-CN/用户管理/为RAM用户授权.md)。

4.  为RAM用户创建AK。

    有关如何为子账户创建AK，请参见[获取AccessKey]()。


**说明：**

创建Velero组件使用的Secret的代码如下。

```
kubectl create ns velero
```

```
kubectl -n velero create secret generic alibaba-addon-secret --from-literal='access-key-id=<your access key id>' --from-literal='access-key-secret=<your access key secret>'
```

您需要将上述代码中`<your access key id>`和`<your access key secret>`替换为您获取的AK信息。

## 步骤二：安装组件

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在集群列表页面，选择目标集群，并在目标集群右侧**操作**列下，选择**更多** \> **系统组件管理**。

3.  找到**migrate-controller**组件，然后单击右侧的**安装**。


## 步骤三：安装Velero

1.  [下载](https://github.com/vmware-tanzu/velero/releases)Velero官方提供的客户端工具。

2.  执行以下命令解压TAR包。

    ```
    tar -xvf <RELEASE-TARBALL-NAME>.tar.gz -C /dir/to/extract/to 
    ```

3.  移动Velero二进制文件至您的可执行路径下。


## 步骤四：创建BackupStorageLocation

BackupStorageLocation用于配置Velero备份的存储位置。

1.  创建名为BackupStorageLocation.yaml文件，然后拷贝以下信息至文件中。

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

    **说明：**

    -   设置`spec.config.region`为OSS Bucket所在地域的信息。
    -   设置`spec.objectStorage.bucket`为OSS Bucket名称。
    -   如果您的环境可以使用OSS内网端点进行数据传输，则可以添加`spec.config.network: internal`。然后ACK默认使用公网传输。
    -   如果您想要使用OSS Bucket下一个指定的子目录，则可以添加`spe.objectStorage.prefix: <your prefix>`。然后ACK默认使用Bucket根目录。
2.  执行以下命令创建BackupStorageLocation。

    ```
    kubectl apply -f BackupStorageLocation.yaml
    ```


## 步骤五：备份应用

1.  部署应用nginx-app-with-pv.yaml。

    1.  创建名为nginx-app-with-pv.yaml的文件。

    2.  使用以下模板部署文件。

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

2.  执行以下命令创建应用。

    ```
    kubectl apply -f nginx-app-with-pv.yam
    ```

3.  执行以下命令查看应用相关信息。

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

4.  备份实例应用所在的整个命名空间。

    -   执行以下命令不备份PV数据。

        ```
        velero backup create nginx-backup-without-pv --include-namespaces nginx-example
        ```

    -   执行以下命令备份PV数据。

        ```
        kubectl -n nginx-example annotate pod/nginx-deployment-7477779c4f-rz95l backup.velero.io/backup-volumes=nginx-logs
        ```

5.  执行以下命令创建备份。

    ```
    velero backup create nginx-backup-without-pv --include-namespaces nginx-example
    ```

6.  执行以下命令查看备份信息。

    ```
    velero backup get
    ```

    ```
    NAME                      STATUS      ERRORS   WARNINGS   CREATED                         EXPIRES   STORAGE LOCATION   SELECTOR
    nginx-backup-without-pv   Completed   0        0          2020-09-18 14:43:13 +0800 CST   29d       default            <none>
    ```


## 步骤六：创建定时备份

如果您需要定时备份应用，则可以使用以下命令创建定时备份。

1.  您需要为备份的Pod Volume添加注释。

    ```
    kubectl -n nginx-example annotate pod/nginx-deployment-7477779c4f-rz95l backup.velero.io/backup-volumes=nginx-logs
    ```

2.  创建备份。

    例如每7天备份一次。

    ```
    velero schedule create nginx-app-with-pv-schedule --schedule "0 7 * * *"
    ```


## 步骤七：恢复应用

本示例模拟故障删除整个命名空间。

1.  执行以下命令删除整个nginx-example命名空间。

    ```
    kubectl delete ns nginx-example
    ```

2.  执行以下命令创建恢复。

    ```
    velero restore create --from-backup nginx-backup-without-pv
    ```


[migrate-controller组件发布记录](/cn.zh-CN/新功能发布记录/组件介绍与变更记录/migrate-controller.md)

  


