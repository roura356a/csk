---
keyword: [NAS, 动态存储卷, 挂载]
---

# 使用NAS动态存储卷

阿里云Kubernetes CSI支持2种类型的NAS动态存储卷挂载：subpath方式和filesystem方式。

使用此方案，需要在集群中部署CSI驱动（Kubernetes集群默认已部署该驱动）。

如果您没有部署CSI-Plugin，请参见[安装CSI插件](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/安装CSI插件.md)。

## subpath类型的NAS动态存储卷

当您的多个Kubernetes应用或者Pod需要挂载相同的NAS存储卷共享数据时，或不同的Pod挂载相同NAS文件系统的不同子目录时， 可以使用subpath类型的NAS动态存储卷方式。

NAS动态存储卷的挂载方式为subpath类型时，您需要手动创建NAS文件系统和挂载点。操作步骤如下。

1.  创建NAS文件系统和挂载点。

    1.  登录[NAS控制台](https://nas.console.aliyun.com/)。

    2.  创建NAS文件系统。请参见[管理文件系统]()。

        ![创建文件系统](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7785659951/p69131.png)

    3.  添加挂载点。请参见[管理挂载点]()。

        ![添加挂载点](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8785659951/p69132.png)

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
          server: "xxxxxxx.cn-hangzhou.nas.aliyuncs.com:/k8s/"
          archiveOnDelete: "true"
        provisioner: nasplugin.csi.alibabacloud.com
        reclaimPolicy: Retain
        ```

        |参数|描述|
        |--|--|
        |mountOptions|挂载NAS的options参数在mountOptions中配置，包括NFS协议版本。|
        |volumeAs|可选subpath、filesystem，分别表示创建子目录类型的PV和文件系统类型PV。|
        |server|表示创建子目录类型的PV时，NAS文件系统的挂载点地址。|
        |reclaimPolicy|PV的回收策略。|
        |archiveOnDelete|表示在reclaimPolicy为Delete时，是否删除后端存储。因为NAS为共享存储，添加此选项进行双重确认。默认为true，表示不会真正删除目录或文件，而是将其Rename，格式为：`archived-{pvName}.{timestamp}`；如果是配置为false，表示会真正删除后端对应的存储资源。|

    2.  执行以下命令创建StorageClass。

        ```
        kubectl create -f alicloud-nas-subpath.yaml
        ```

3.  创建PV、PVC，和Pod挂载NAS存储卷。

    创建Pod `nginx-1`和`nginx-2`共享NAS存储卷的同一个子目录，`pvc.yaml`、`nginx-1.yaml`、`nginx-2.yaml`文件内容如下。

    `pvc.yaml`

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

    `nginx-1.yaml`：

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

    `nginx-2.yaml`：

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

    执行以下命令，创建并查看PVC和Deployment。

    ```
    kubectl create -f pvc.yaml -f nginx-1.yaml -f nginx-2.yaml
    ```

    执行以下命令查看Pod信息。

    ```
    kubectl get pod
    ```

    返回结果如下：

    ```
    NAME                                READY   STATUS    RESTARTS   AGE
    deployment-nas-1-5b5cdb85f6-nhklx   1/1     Running   0          32s
    deployment-nas-2-c5bb4746c-4jw5l    1/1     Running   0          32s
    ```

    **说明：** NAS存储卷的`xxxxxxx.cn-hangzhou.nas.aliyuncs.com:/share/nas-79438493-f3e0-11e9-bbe5-00163e09c2be`会同时挂载到`deployment-nas-1-5b5cdb85f6-nhklx`和`deployment-nas-2-c5bb4746c-4jw5l`/data目录下。其中：

    -   `/share`：StorageClass中指定的subpath。
    -   `nas-79438493-f3e0-11e9-bbe5-00163e09c2be`：PV的名称。
    如果您需要为不同的Pod挂载同一个NAS文件系统的不同子目录， 则需要分别创建pvc-1和nginx-1以及pvc-2和nginx-2。


## filesystem类型的NAS动态存储卷

**说明：** filesystem类型的NAS动态卷在删除时默认保留文件系统和挂载点， 若需要在释放PV资源的同时释放NAS文件系统和挂载点， 则需要同时设置StorageClass中的reclaimPolicy为Delete且deleteVolume的值为**true**。

当您的Kubernetes应用需要动态创建和删除NAS文件系统和挂载点时， 可以使用filesystem类型。

使用filesystem类型NAS存储卷的Pod只能创建一个文件系统和一个挂载点， 多个Pod之间无法共享一个存储卷。操作步骤如下。

1.  RAM Policy设置和授予。

    filesystem类型的NAS存储卷涉及NAS文件系统和挂载点的动态创建与删除， 需要授予csi-nasprovisioner相应的权限，RAM Policy的最小集合如下。

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

        ![自定义授权](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8785659951/p69183.png)

        **说明：** 托管集群是自动添加Master RAM，专有集群则需要加Master的RAM。

    -   创建子账号授权以上RAM Policy并生成AccessKey，配置到StatefulSet csi-provisioner中csi-nasprovisioner的env变量中。请参见[容器服务默认角色](/cn.zh-CN/Kubernetes集群用户指南/授权/容器服务默认角色.md)。

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

    1.  创建并拷贝以下内容到alicloud-nas-fs.yaml文件中。

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
          vpcId: "vpc-xxxxxxxxxxxx"
          vSwitchId: "vsw-xxxxxxxxx"
          deleteVolume: "false"
        provisioner: nasplugin.csi.alibabacloud.com
        reclaimPolicy: Retain
        ```

        |参数|描述|
        |--|--|
        |volumeAs|定义创建数据的类型，可选filesystem和subpath两种类型：         -   filesystem表示provisioner自动创建NAS文件系统，一个PV对应一个NAS文件系统。
        -   subpath类型表示一个PV对应一个NAS文件系统的子目录，provisioner自动创建NAS文件系统子目录。 |
        |storageType|定义创建NAS文件系统的类型，可选Performance、Capacity两种类型，分别表示性能型、容量型。默认性能型。|
        |zoneId|定义创建NAS文件系统所在可用区。|
        |vpcId|定义创建NAS文件系统对应挂载点所在VPC。|
        |vSwitchId|定义创建NAS文件系统对应挂载点所在vSwitch ID。|
        |accessGroupName|定义创建NAS文件系统对应挂载点所用的AccessGroup。默认：DEFAULT\_VPC\_GROUP\_NAME。|
        |deleteVolume|定义数据卷删除时处理NAS文件系统策略，由于NAS为共享文件系统，安全起见需要同时配置。|
        |reclaimPolicy|Delete、且deleteVolume为true才会在删除PVC的时候把NAS文件系统删除。|

    2.  执行以下命令创建StorageClass。

        ```
        kubectl create -f alicloud-nas-fs.yaml
        ```

3.  创建PV、PVC，和Pod挂载NAS存储卷。

    `pvc.yaml`、`nginx.yaml`文件内容如下。

    `pvc.yaml`

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

    `nginx.yaml`

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

    执行以下命令，创建PVC和Deployment。

    ```
    kubectl create -f pvc.yaml -f nginx.yaml
    ```


这种场景下，CSI会在PVC创建时动态新建NAS文件系统和挂载点，PVC删除时动态删除挂载点和文件系统。

