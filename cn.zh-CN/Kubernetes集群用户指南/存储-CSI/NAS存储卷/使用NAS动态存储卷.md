---
keyword: [NAS, 动态存储卷, 持久化存储, 共享存储]
---

# 使用NAS动态存储卷

阿里云Kubernetes CSI支持2种类型的NAS动态存储卷挂载：subpath方式和filesystem方式。本文介绍如何使用阿里云NAS动态存储卷，及使用NAS动态存储卷如何实现持久化存储与共享存储。

-   已创建Kubernetes集群。具体操作，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   已创建动态NAS卷。请参见[创建文件系统]()。

    若需要加密NAS存储卷中的数据，创建NAS文件系统时请配置加密类型。

-   已创建NAS挂载点。请参见[管理挂载点]()。

    NAS挂载点需要和集群节点在同一个VPC内。


## 使用场景

-   对磁盘I/O要求较高的应用。
-   读写性能相对于对象存储OSS高。
-   可实现跨主机文件共享，例如可作为文件服务器。

## 注意事项

-   在使用极速NAS文件系统时，配置动态存储卷StorageClass中的`path`需要以/share为父目录。例如，`0cd8b4a576-g****.cn-hangzhou.nas.aliyuncs.com:/share/subpath`表示Pod挂载的NAS文件系统子目录为`/share/subpath`。
-   NAS支持同时被多个Pod挂载，此时多个Pod可能同时修改相同数据，需要应用自行实现数据的同步。

    **说明：** NAS存储的/目录不支持修改权限、属主和属组。


## 使用subpath类型的NAS动态存储卷

当您的多个Kubernetes应用或者Pod需要挂载相同的NAS存储卷共享数据时，或不同的Pod挂载相同NAS文件系统的不同子目录时， 可以使用subpath类型的NAS动态存储卷方式。

NAS动态存储卷的挂载方式为subpath类型时，您需要手动创建NAS文件系统和挂载点。

1.  创建NAS文件系统和挂载点。

    1.  登录[NAS控制台](https://nas.console.aliyun.com/)。

    2.  创建NAS文件系统。请参见[创建文件系统]()。

    3.  添加挂载点。请参见[管理挂载点]()。

2.  创建StorageClass。

    1.  创建并复制以下内容到alicloud-nas-subpath.yaml文件中。

        ```
        apiVersion: storage.k8s.io/v1
        kind: StorageClass
        metadata:
          name: alicloud-nas-subpath
        mountOptions:
        - nolock,tcp,noresvport
        - vers=3
        parameters:
          volumeAs: subpath
          server: "0cd8b4a576-g****.cn-hangzhou.nas.aliyuncs.com:/k8s/"
        provisioner: nasplugin.csi.alibabacloud.com
        reclaimPolicy: Retain
        ```

        |参数|描述|
        |--|--|
        |mountOptions|挂载NAS的options参数在mountOptions中配置，包括NFS协议版本。|
        |volumeAs|可选subpath、filesystem，分别表示创建子目录类型的PV和文件系统类型PV。|
        |server|表示创建子目录类型的PV时，NAS文件系统的挂载点地址。|
        |reclaimPolicy|PV的回收策略，默认为Delete，支持Retain。        -   Delete模式：删除PVC的时候，PV和NAS文件系统会一起删除。
        -   Retain模式：删除PVC的时候，PV和NAS文件系统不会被删除，需要您手动删除。
如果数据安全性要求高，推荐使用Retain方式以免误删数据。|
        |archiveOnDelete|表示在reclaimPolicy为Delete时，是否删除后端存储。因为NAS为共享存储，添加此选项进行双重确认。在参数parameters下配置。默认为true，表示不会真正删除目录或文件，而是将其Rename，格式为：`archived-{pvName}.{timestamp}`；如果是配置为false，表示会真正删除后端对应的存储资源。**说明：** 在业务流量非常大时，不适合配置为false。更多信息，请参见[使用NAS动态存储卷时Controller的任务队列已满且无法创建新的PV](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/NAS存储卷/NAS存储卷FAQ.md)。 |

    2.  执行以下命令创建StorageClass。

        ```
        kubectl create -f alicloud-nas-subpath.yaml
        ```

3.  执行以下命令创建PVC。

    1.  创建并复制以下内容到pvc.yaml文件中。

        ```
        kind: PersistentVolumeClaim
        apiVersion: v1
        metadata: 
          name: nas-csi-pvc
        spec:
          accessModes:
          - ReadWriteMany 
          storageClassName: alicloud-nas-subpath
          resources: 
            requests:
              storage: 20Gi
        ```

        |参数|说明|
        |--|--|
        |`name`|PVC的名称。|
        |`accessModes`|配置访问模式。|
        |`storageClassName`|StorageClass的名称，用于绑定StorageClass。|
        |`storage`|声明应用存储使用量。|

    2.  执行以下命令创建PVC。

        ```
        kubectl create -f pvc.yaml
        ```

4.  执行以下命令创建应用。

    创建应用**nginx-1**和**nginx-2**共享NAS存储卷的同一个子目录。

    1.  创建并复制以下内容到nginx-1.yaml文件中。

        ```
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: deployment-nas-1
          labels:
            app: nginx-1
        spec:
          selector:
            matchLabels:
              app: nginx-1
          template:
            metadata:
              labels:
                app: nginx-1
            spec:
              containers:
              - name: nginx
                image: nginx:1.7.9
                ports:
                - containerPort: 80
                volumeMounts:
                  - name: nas-pvc
                    mountPath: "/data"
              volumes:
                - name: nas-pvc
                  persistentVolumeClaim:
                    claimName: nas-csi-pvc
        ```

        -   `mountPath`：NAS在容器中挂载的位置。
        -   `claimName`：PVC的名称，用于绑定PVC。本例中为**nas-csi-pvc**。
    2.  创建并复制以下内容到nginx-2.yaml文件中。

        ```
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: deployment-nas-2
          labels:
            app: nginx-2
        spec:
          selector:
            matchLabels:
              app: nginx-2
          template:
            metadata:
              labels:
                app: nginx-2
            spec:
              containers:
              - name: nginx
                image: nginx:1.7.9
                ports:
                - containerPort: 80
                volumeMounts:
                  - name: nas-pvc
                    mountPath: "/data"
              volumes:
                - name: nas-pvc
                  persistentVolumeClaim:
                    claimName: nas-csi-pvc
        ```

        -   `mountPath`：NAS在容器中挂载的位置。本例为/data。
        -   `claimName`：输入与**nginx-1**应用相同的PVC名称，本例为**nas-csi-pvc**。
    3.  执行以下命令创建应用**nginx-1**和**nginx-2**。

        ```
        kubectl create -f nginx-1.yaml -f nginx-2.yaml
        ```

5.  执行以下命令查看Pod信息。

    ```
    kubectl get pod
    ```

    预期输出：

    ```
    NAME                                READY   STATUS    RESTARTS   AGE
    deployment-nas-1-5b5cdb85f6-n****   1/1     Running   0          32s
    deployment-nas-2-c5bb4746c-4****    1/1     Running   0          32s
    ```

    **说明：** NAS存储卷的`0cd8b4a576-g****.cn-hangzhou.nas.aliyuncs.com:/share/nas-79438493-f3e0-11e9-bbe5-00163e09****`会同时挂载到`deployment-nas-1-5b5cdb85f6-n****`和`deployment-nas-2-c5bb4746c-4****`的/data目录下。其中：

    -   `/share`：StorageClass中指定的subpath。
    -   `nas-79438493-f3e0-11e9-bbe5-00163e09****`：PV的名称。
    如果您需要为不同的Pod挂载同一个NAS文件系统的不同子目录， 则需要分别创建**pvc-1**和**nginx-1**以及**pvc-2**和**nginx-2**。


## 使用filesystem类型的NAS动态存储卷

**说明：** filesystem类型的NAS动态卷在删除时默认保留文件系统和挂载点， 若需要在释放PV资源的同时释放NAS文件系统和挂载点， 则需要同时设置StorageClass中的reclaimPolicy为Delete且deleteVolume的值为true。

当您的Kubernetes应用需要动态创建和删除NAS文件系统和挂载点时， 可以使用filesystem类型。

使用filesystem类型NAS存储卷的Pod只能创建一个文件系统和一个挂载点， 多个Pod之间无法共享一个存储卷。操作步骤如下。

1.  RAM Policy设置和授予。

    filesystem类型的NAS存储卷涉及NAS文件系统和挂载点的动态创建与删除， 需要授予csi-provisioner相应的权限，RAM Policy的最小集合如下。

    ```
    {
        "Action": [
            "nas:DescribeMountTargets",
            "nas:CreateMountTarget",
            "nas:DeleteFileSystem",
            "nas:DeleteMountTarget",
            "nas:CreateFileSystem"
        ],
        "Resource": [
            "*"
        ],
            "Effect": "Allow"
    }
    ```

    您可以通过以下任意一种方式进行授权：

    -   编辑Kubernetes集群的Master RAM角色中的自定义策略内容，添加以上NAS相关的权限设置。请参见[容器服务默认角色](/cn.zh-CN/Kubernetes集群用户指南/授权/容器服务默认角色.md)。

        ![自定义授权](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/5536388261/p69183.png)

        **说明：** 托管集群是自动添加Master RAM，专有集群则需要加Master RAM。

    -   创建RAM用户授权以上RAM Policy并生成AccessKey，配置到csi-provisioner的`env`变量中。请参见[容器服务默认角色](/cn.zh-CN/Kubernetes集群用户指南/授权/容器服务默认角色.md)。

        ```
        env:
            - name: CSI_ENDPOINT
                value: unix://socketDir/csi.sock
            - name: ACCESS_KEY_ID
                value: ""
            - name: ACCESS_KEY_SECRET
                value: ""
        ```

2.  创建StorageClass。

    1.  创建并复制以下内容到alicloud-nas-fs.yaml文件中。

        ```
        apiVersion: storage.k8s.io/v1
        kind: StorageClass
        metadata:
          name: alicloud-nas-fs
        mountOptions:
        - nolock,tcp,noresvport
        - vers=3
        parameters:
          volumeAs: filesystem
          zoneId: cn-hangzhou-a
          vpcId: "vpc-2ze9c51qb5kp1nfqu****"
          vSwitchId: "vsw-gw8tk6gecif0eu9ky****"
          deleteVolume: "false"
        provisioner: nasplugin.csi.alibabacloud.com
        reclaimPolicy: Retain
        ```

        |参数|描述|
        |--|--|
        |volumeAs|定义创建数据的类型，可选filesystem和subpath两种类型：         -   filesystem表示Provisioner自动创建NAS文件系统，一个PV对应一个NAS文件系统。
        -   subpath类型表示一个PV对应一个NAS文件系统的子目录，Provisioner自动创建NAS文件系统子目录。 |
        |storageType|定义创建NAS文件系统的类型，可选**Performance**及**Capacity**两种类型，分别表示性能型、容量型。默认为性能型。|
        |zoneId|定义创建NAS文件系统所在可用区。|
        |vpcId|定义创建NAS文件系统对应挂载点所在VPC。|
        |vSwitchId|定义创建NAS文件系统对应挂载点所在vSwitch ID。|
        |accessGroupName|定义创建NAS文件系统对应挂载点所用的AccessGroup。默认为DEFAULT\_VPC\_GROUP\_NAME。|
        |deleteVolume|定义数据卷删除时处理NAS文件系统策略，由于NAS为共享文件系统，安全起见需要同时配置。|
        |reclaimPolicy|PV的回收策略。当值为Delete且deleteVolume为true才会在删除PVC的时候把NAS文件系统删除。|

    2.  执行以下命令创建StorageClass。

        ```
        kubectl create -f alicloud-nas-fs.yaml
        ```

3.  创建PVC和Pod挂载NAS存储卷。

    1.  创建并复制以下内容到pvc.yaml文件中。

        ```
        kind: PersistentVolumeClaim
        apiVersion: v1
        metadata:
          name: nas-csi-pvc-fs
        spec:
          accessModes:
            - ReadWriteMany
          storageClassName: alicloud-nas-fs
          resources:
            requests:
              storage: 20Gi
        ```

    2.  创建并复制以下内容到nginx.yaml文件中。

        ```
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: deployment-nas-fs
          labels:
            app: nginx
        spec:
          selector:
            matchLabels:
              app: nginx
          template:
            metadata:
              labels:
                app: nginx
            spec:
              containers:
              - name: nginx
                image: nginx:1.7.9
                ports:
                - containerPort: 80
                volumeMounts:
                  - name: nas-pvc
                    mountPath: "/data"
              volumes:
                - name: nas-pvc
                  persistentVolumeClaim:
                    claimName: nas-csi-pvc-fs
        ```

    3.  执行以下命令创建PVC和Pod。

        ```
        kubectl create -f pvc.yaml -f nginx.yaml
        ```


这种场景下，CSI会在PVC创建时动态新建NAS文件系统和挂载点，PVC删除时动态删除挂载点和文件系统。

## 验证NAS的持久化存储

1.  查看部署应用的Pod和NAS文件。

    1.  执行以下命令，查看部署的应用所在Pod的名称。

        ```
        kubectl get pod 
        ```

        预期输出：

        ```
        NAME                                READY   STATUS    RESTARTS   AGE
        deployment-nas-1-5b5cdb85f6-n****   1/1     Running   0          32s
        deployment-nas-2-c5bb4746c-4****    1/1     Running   0          32s
        ```

    2.  执行以下命令，查看任意一个Pod/data路径下的文件，本文以名为`deployment-nas-1-5b5cdb85f6-n****`的Pod为例。

        ```
        kubectl exec deployment-nas-1-5b5cdb85f6-n**** -- ls /data
        ```

        无返回结果，说明/data路径下无文件。

2.  执行以下命令，在名为`deployment-nas-1-5b5cdb85f6-n****`的Pod/data路径下创建文件nas。

    ```
    kubectl exec deployment-nas-1-5b5cdb85f6-n**** -- touch /data/nas
    ```

3.  执行以下命令，查看名为`deployment-nas-1-5b5cdb85f6-n****`的Pod/data路径下的文件。

    ```
    kubectl exec deployment-nas-1-5b5cdb85f6-n**** -- ls /data
    ```

    预期输出：

    ```
    nas
    ```

4.  执行以下命令，删除Pod。

    ```
    kubectl delete pod deployment-nas-1-5b5cdb85f6-n****
    ```

5.  同时在另一个窗口中，执行以下命令，查看Pod删除及Kubernetes重建Pod的过程。

    ```
    kubectl get pod -w -l app=nginx
    ```

6.  验证删除Pod后，NAS里创建的文件是否还存在。

    1.  执行以下命令，查看Kubernetes重建的Pod名称。

        ```
        kubectl get pod
        ```

        预期输出：

        ```
        NAME                                READY   STATUS    RESTARTS   AGE
        deployment-nas-1-5b5cdb85f6-n****   1/1     Running   0          32s
        deployment-nas-2-c5bb4746c-4****    1/1     Running   0          32s
        ```

    2.  执行以下命令，查看名为`deployment-nas-1-5b5cdb85f6-n****`的Pod/data路径下的文件。

        ```
        kubectl exec deployment-nas-1-5b5cdb85f6-n**** -- ls /data
        ```

        预期输出：

        ```
        nas
        ```

        nas文件仍然存在，说明NAS的数据可持久化保存。


## 验证NAS的共享存储

1.  查看部署的应用所在的Pod和NAS文件。

    1.  执行以下命令，查看应用所在Pod的名称。

        ```
        kubectl get pod 
        ```

        预期输出：

        ```
        NAME                                READY   STATUS    RESTARTS   AGE
        deployment-nas-1-5b5cdb85f6-n****   1/1     Running   0          32s
        deployment-nas-2-c5bb4746c-4****    1/1     Running   0          32s
        ```

    2.  执行以下命令，查看2个Pod/data路径下的文件。

        ```
        kubectl exec deployment-nas-1-5b5cdb85f6-n**** -- ls /data
        kubectl exec deployment-nas-2-c5bb4746c-4**** -- ls /data
        ```

2.  执行以下命令，在任意一个Pod的/data路径下创建文件nas。

    ```
     kubectl exec deployment-nas-1-5b5cdb85f6-n**** -- touch /data/nas
    ```

3.  执行以下命令，查看2个Pod/data路径下的文件。

    1.  执行以下命令，查看名为`deployment-nas-1-5b5cdb85f6-n****`的Pod/data路径下的文件。

        ```
        kubectl exec deployment-nas-1-5b5cdb85f6-n**** -- ls /data
        ```

        预期输出：

        ```
        nas
        ```

    2.  执行以下命令，查看名为`deployment-nas-2-c5bb4746c-4****`的Pod/data路径下的文件。

        ```
        kubectl exec deployment-nas-2-c5bb4746c-4**** -- ls /data
        ```

        预期输出：

        ```
        nas
        ```

        在任意一个Pod的/data下创建的文件，两个Pod下的/data路径下均存在此文件，说明两个Pod共享一个NAS。


