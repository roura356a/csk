---
keyword: [Migrate Controller, 跨集群, 迁移, 备份]
---

# 使用Migrate Controller跨集群迁移应用

Migrate Controller是ACK基于开源项目Velero开发的一个Kubernetes应用迁移的组件。您可以使用Migrate Controller的Velero工具来跨集群迁移应用。本文通过示例演示如何跨集群迁移应用。

[安装Migrate Controller](/intl.zh-CN/Kubernetes集群用户指南/多云混合云管理/组件管理/安装和使用Migrate Controller.md)

## 迁移步骤

本文假设您有cluster01和cluster02两个集群，希望从cluster01中将命名空间nginx-examples下的应用迁移到cluster02。您需要按照以下步骤进行应用迁移。

1.  在cluster01集群中，安装Velero客户端和Migrate Controller组件，并配置连接OSS名为mybackups的Bucket。具体步骤，请参见[安装Migrate Controller](/intl.zh-CN/Kubernetes集群用户指南/多云混合云管理/组件管理/安装和使用Migrate Controller.md)。

2.  与步骤1相同，在cluster02集群中安装Velero客户端和Migrate Controller组件，并配置连接OSS名为mybackups的Bucket。具体步骤，请参见[安装Migrate Controller](/intl.zh-CN/Kubernetes集群用户指南/多云混合云管理/组件管理/安装和使用Migrate Controller.md)。

3.  在cluster01集群中备份命名空间nginx-examples下的应用，例如生成备份名称为nginx-backup。

4.  在cluster02集群中引用备份nginx-backup并创建恢复应用，完成应用从cluster01迁移到cluster02的流程。


## 迁移示例

本文通过在cluster01集群中备份应用和在cluster02集群中恢复应用的示例，具体说明如何完成应用的迁移操作。在备份和恢复应用前，您需完成[迁移流程](#migrate)中步骤1和2的操作。备份和恢复应用的操作如下。

1.  部署示例应用nginx-app-with-pv。

    1.  创建名为nginx-app-with-pv.yaml的文件，并将以下YAML内容拷贝至文件中。

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

    2.  执行以下命令部署应用至cluster01集群中。

        ```
        kubectl apply -f nginx-app-with-pv.yam
        ```

2.  执行以下命令查看应用相关信息。

    查看Deployment命令如下：

    ```
    kubectl -n nginx-example get deployment
    ```

    输出：

    ```
    NAME                                     READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.extensions/nginx-deployment   1/1     1            1           108s
    ```

    查看Pod命令如下：

    ```
    kubectl -n nginx-example get pod
    ```

    输出：

    ```
    NAME                                    READY   STATUS    RESTARTS   AGE
    pod/nginx-deployment-7477779c4f-rz95l   2/2     Running   0          108s
    ```

    查看PVC命令如下：

    ```
    kubectl -n nginx-example get pvc
    ```

    输出：

    ```
    NAME                               STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS        AGE
    persistentvolumeclaim/nginx-logs   Bound    d-bp1eutshfe774exa5os2   20Gi       RWO            alicloud-disk-ssd   108s
    ```

3.  备份示例应用所在的整个命名空间。

    -   备份应用所在的整个命名空间（不备份PV数据）的命令如下。

        ```
        velero backup create nginx-backup-without-pv --include-namespaces nginx-example
        ```

    -   备份应用所在的整个命名空间（备份PV数据）的操作如下。
        1.  您首先为需要备份的Pod存储卷添加注解。命令如下：

            ```
            kubectl -n nginx-example annotate pod/nginx-deployment-7477779c4f-rz95l backup.velero.io/backup-volumes=nginx-logs
            ```

        2.  创建备份。命令如下：

            ```
            velero backup create nginx-backup-with-pv --include-namespaces nginx-example
            ```

4.  执行以下命令查看备份的应用信息。

    ```
    velero backup get
    ```

    输出（不备份PV数据）：

    ```
    NAME                      STATUS      ERRORS   WARNINGS   CREATED                         EXPIRES   STORAGE LOCATION   SELECTOR
    nginx-backup-without-pv   Completed   0        0          2020-09-18 14:43:13 +0800 CST   29d       default            <none>
    ```

    可以看出您已成功创建备份应用。

5.  使用以下命令在cluster02集群恢复应用（不备份PV数据）以完成迁移操作。

    ```
    velero restore create --from-backup nginx-backup-without-pv
    ```


**相关文档**  


[使用Migrate Controller备份和恢复应用]()

