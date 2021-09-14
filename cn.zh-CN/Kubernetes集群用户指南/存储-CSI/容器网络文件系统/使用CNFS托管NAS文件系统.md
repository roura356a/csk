---
keyword: [容器网络文件系统CNFS, NAS文件系统]
---

# 使用CNFS托管NAS文件系统

为了提升NAS文件系统的性能，容器服务ACK使用CNFS（Container Network File System）托管NAS文件系统，对文件系统层独立管理。本文介绍如何使用CNFS托管NAS文件系统及CNFS在工作负载中的应用。

-   已创建ACK集群。具体操作，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   已通过kubectl连接集群。具体操作，请参见[通过kubectl工具连接集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl工具连接集群.md)。

## 功能介绍

可通过以下三种方式使用CNFS托管NAS文件系统：

-   [方式一：使用CNFS创建默认NAS文件系统](#section_n2s_x85_7fu)

    创建一个默认的CNFS文件系统，并使用NAS动态存储卷自动挂载默认创建的NAS文件系统，同时将NAS动态存储卷绑定到工作负载Deployment和StatefulSet中。

-   [方式二：使用CNFS创建自定义NAS文件系统](#section_16b_q45_hx9)

    使用CNFS创建自定义的NAS文件系统，并使用NAS静态存储卷或NAS动态存储卷挂载自定义创建的NAS文件系统，然后将创建的NAS静态存储卷或NAS动态存储卷绑定到工作负载Deployment中。

-   [方式三：使用已有的NAS文件系统创建CNFS](#section_qqn_h8b_5u9)

    使用CNFS配置已有的NAS文件系统，并使用NAS静态存储卷或NAS动态存储卷挂载已有的NAS文件系统，然后将创建的NAS静态存储卷或NAS动态存储卷绑定到工作负载Deployment中。


## 方式一：使用CNFS创建默认NAS文件系统

执行以下内容，创建一个默认的CNFS文件系统，并使用NAS动态存储卷自动挂载到默认创建的NAS文件系统中，同时将NAS动态存储卷绑定到工作负载Deployment和StatefulSet中。

```
# 创建CNFS、storageclass和deployment、statefulset对象。
cat << EOF | kubectl apply -f -
apiVersion: storage.alibabacloud.com/v1beta1
kind: ContainerNetworkFileSystem
metadata:
  name: cnfs-nas-filesystem
spec:
  description: "cnfs"
  type: nas
  reclaimPolicy: Retain #只支持Retain策略,删除CNFS时并不会删除NAS文件系统。
  parameters:
    encryptType: SSE-KMS #可选参数,不指定表示对文件不使用NAS托管加密,指定SSE-KMS表示开启此功能。
    enableTrashCan: "true" #可选参数,不指定表示不打开回收站功能,指定true表示开启此功能。
---
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: alibabacloud-cnfs-nas
mountOptions:
  - nolock,tcp,noresvport
  - vers=3
parameters:
  volumeAs: subpath
  containerNetworkFileSystem: cnfs-nas-filesystem
  path: "/"
provisioner: nasplugin.csi.alibabacloud.com
reclaimPolicy: Retain
allowVolumeExpansion: true #可选参数,指定为true表示允许对NAS文件系统进行扩容。
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: cnfs-nas-pvc
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: alibabacloud-cnfs-nas
  resources:
    requests:
      storage: 70Gi #如果打开目录限额功能,则storage字段会生效,动态创建目录写入数据量最大为70 GiB。
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: cnfs-nas-deployment
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
        image: nginx:1.14.2
        volumeMounts:
        - mountPath: "/data"
          name: cnfs-nas-pvc
      volumes:
      - name: cnfs-nas-pvc
        persistentVolumeClaim:
          claimName: cnfs-nas-pvc
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: cnfs-nas-sts
  labels:
    app: nginx
spec:
  serviceName: "nginx"
  replicas: 2
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
        image: nginx:1.14.2
        volumeMounts:
        - mountPath: "/data"
          name: www
  volumeClaimTemplates:
  - metadata:
      name: www
    spec:
      accessModes: [ "ReadWriteOnce" ]
      storageClassName: "alibabacloud-cnfs-nas"
      resources:
        requests:
          storage: 50Gi #如果打开目录限额功能,则storage字段会生效,动态创建目录写入数据量最大为50 GiB。
EOF
```

**说明：** 根据当前集群所在的VPC创建NAS。若当前地域不支持容量型NAS，则创建性能型NAS，默认对文件不进行加密处理。

## 方式二：使用CNFS创建自定义NAS文件系统

使用CNFS创建自定义的NAS文件系统，并使用NAS静态存储卷或NAS动态存储卷挂载自定义创建的NAS文件系统，然后将创建的NAS静态存储卷或NAS动态存储卷绑定到工作负载Deployment中。

1.  创建自定义的NAS文件系统。

    1.  执行以下命令创建自定义的NAS文件系统。

        ```
        cat <<EOF | kubectl apply -f -
        apiVersion: storage.alibabacloud.com/v1beta1
        kind: ContainerNetworkFileSystem
        metadata:
          name: cnfs-nas-filesystem
        spec:
          description: "cnfs"
          type: nas
          reclaimPolicy: Retain
          parameters:
            filesystemType: standard
            storageType: Capacity
            protocolType: NFS
            encryptType: SSE-KMS
            enableTrashCan: true
            trashCanReservedDays: 5
            vSwitchId: vsw-2ze9l3ppwzg6bl02j****
        EOF
        ```

        |参数|说明|
        |--|--|
        |description|当前文件系统的简单描述。|
        |type|需要创建的存储卷类型。|
        |reclaimPolicy|回收策略，目前仅支持Retain策略，删除CNFS时并不会删除NAS文件系统。|
        |parameters.filesystemType|文件系统类型。默认为通用型standard。|
        |parameters.storageType|存储类型，当filesystemType为standard时，可选择性能型Performance或容量型CapacityNAS文件系统。|
        |parameters.protocolType|文件传输协议类型为NFS，目前仅支持NFS V3协议。|
        |parameters.encryptType|加密方式，None表示不加密，SSE-KMS表示使用NAS服务端KMS加密。|
        |parameters.enableTrashCan|是否开启回收站功能，false表示不开启回收站功能，true表示开启回收站功能。|
        |parameters.trashCanReservedDays|表示回收站文件保留的最长时间，默认为7天。示例中`trashCanReservedDays: 5`，表示回收站的文件最长保留5天。|
        |parameters.vSwitchId|创建出的NAS文件系统需要使用的交换机ID。|

    2.  执行以下命令查看创建的NAS文件系统。

        ```
        kubectl get cnfs
        ```

        预期输出：

        ```
        NAME                  AGE
        cnfs-nas-filesystem   6d
        ```

    3.  执行以下命令查看NAS文件系统的详细信息。

        ```
        kubectl get cnfs/cnfs-nas-filesystem -o yaml
        ```

        预期输出：

        ```
        apiVersion: storage.alibabacloud.com/v1beta1
        kind: ContainerNetworkFileSystem
        metadata:
          annotations:
            kubectl.kubernetes.io/last-applied-configuration: |
              {"apiVersion":"storage.alibabacloud.com/v1beta1","kind":"ContainerNetworkFileSystem","metadata":{"annotations":{},"name":"nas-load-mount-target"},"spec":{"description":"filesystem4","parameters":{"filesystemId":"17f7e4****","server":"17f7e4****-hlm35.cn-beijing.nas.aliyuncs.com"},"reclaimPolicy":"Retain","type":"nas"}}
          creationTimestamp: "2021-05-14T08:20:09Z"
          finalizers:
          - protection.alibabacloud.com/cnfs
          generation: 6
          name: cnfs-nas-filesystem
          resourceVersion: "122342382"
          selfLink: /apis/storage.alibabacloud.com/v1beta1/containernetworkfilesystems/nas-load-mount-target
          uid: a9e9650c-68b2-405b-8274-0f5b6063****
        spec:
          description: "cnfs"
          type: nas
          reclaimPolicy: Retain
          parameters:
            filesystemType: standard
            storageType: Capacity
            protocolType: NFS
            encryptType: SSE-KMS
            vSwitchId: vsw-XXX
            enableTrashCan: true
        status:
          conditions:
          - lastProbeTime: "2021-05-14 16:20:15"
            reason: The nas filesystem and mount target complete initialization.
            status: Ready
          fsAttributes:
            accessGroupName: DEFAULT_VPC_GROUP_NAME
            encryptType: SSE-KMS
            enableTrashCan: true
            filesystemId: 17f7e48ece
            filesystemType: standard
            protocolType: NFS
            regionId: cn-beijing
            server: 17f7e48ece-hlm35.cn-beijing.nas.aliyuncs.com
            storageType: Capacity
            vSwitchId: vsw-2ze9l3ppwzg6bl02j****
            vpcId: vpc-2ze9sgmehjvwv5x74****
            zoneId: cn-beijing-h
          status: Available
        ```

        |参数|说明|
        |--|--|
        |status|CNFS的当前状态，包括Pending（等待）、Creating（创建中）、Initialization（创建文件系统中）、Available（可用）、Unavailable（暂时不可用，可以恢复为可用状态）、Fatal（不可用，无法恢复）、Terminating（删除中）。|
        |conditions.lastProbeTime|最后探活的时间。|
        |conditions.reason|处于当前状态的原因。|
        |conditions.status|当前状态是否可用，可用为Ready，不可用为NotReady。|
        |fsAttributes.accessGroupName|挂载点所应用的权限组名称，目前仅支持DEFAULT\_VPC\_GROUP\_NAME（专有网络默认权限组）。|
        |fsAttributes.encryptType|加密方式，None表示不加密，SSE-KMS表示使用NAS全托管的服务端KMS加密。|
        |fsAttributes.enableTrashCan|是否开启回收站功能，false表示不开启回收站功能，true表示开启回收站功能。|
        |fsAttributes.filesystemId|文件系统ID。|
        |fsAttributes.filesystemType|文件系统类型。默认为通用型standard。|
        |fsAttributes.protocolType|文件传输协议类型。目前支持NFS协议。|
        |fsAttributes.regionId|CNFS所在的地域。|
        |fsAttributes.server|CNFS的挂载点Domain。|
        |fsAttributes.storageType|存储类型，当filesystemType为standard时，可选择性能型Performance或容量型CapacityNAS文件系统。|
        |fsAttributes.vSwitchId|CNFS使用的vSwitch。|
        |fsAttributes.vpcId|CNFS所在的VPC。|
        |fsAttributes.zoneId|CNFS所在的Zone。|

2.  通过PV绑定NAS文件系统。

    通过创建静态PV或动态PV绑定NAS文件系统：

    -   创建静态PV。
        1.  执行以下内容创建引用NAS文件系统的PV对象。

            ```
            cat <<EOF | kubectl apply -f -
            apiVersion: v1
            kind: PersistentVolume
            metadata:
              name: cnfs-nas-pv
              labels:
                alicloud-pvname: cnfs-nas-pv
            spec:
              capacity:
                storage: 5Gi
              accessModes:
                - ReadWriteMany
              csi:
                driver: nasplugin.csi.alibabacloud.com
                volumeHandle: cnfs-nas-pv #需要与pv的name保持一致
                volumeAttributes:
                  containerNetworkFileSystem: cnfs-nas-filesystem
                  path: "/"
                  mode: "644"
              mountOptions:
                - nolock,tcp,noresvport
                - vers=3
            EOF
            ```

            |参数|说明|
            |--|--|
            |containerNetworkFileSystem|指定需要使用的CNFS名称。|
            |path|挂载存储卷在CNFS中使用的路径。|

        2.  执行以下命令查看PV是否创建成功。

            ```
            kubectl get pv
            ```

            预期输出：

            ```
            NAME          CAPACITY   ACCESS MODES     RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
            cnfs-nas-pv   5Gi        RWX              Retain           Available                                   4s
            ```

    -   创建动态PV。
        1.  执行以下内容创建引用NAS文件系统的PV对象。

            ```
            cat <<EOF | kubectl apply -f -
            apiVersion: storage.k8s.io/v1
            kind: StorageClass
            metadata:
              name: alibabacloud-nas-cnfs
            mountOptions:
              - nolock,tcp,noresvport
              - vers=3
            parameters:
              volumeAs: subpath
              containerNetworkFileSystem: nas-load-mount-target
              path: "/"
            provisioner: nasplugin.csi.alibabacloud.com
            reclaimPolicy: Retain
            allowVolumeExpansion: true
            EOF
            ```

            **说明：** allowVolumeExpansion：取值为true或false。表示是否开启Quota及扩容功能。

        2.  执行以下命令查看PV是否创建成功。

            ```
            kubectl get pv
            ```

            预期输出：

            ```
            NAME          CAPACITY   ACCESS MODES     RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
            cnfs-nas-pv   5Gi        RWX              Retain           Available                                   4s
            ```

3.  创建PVC。

    1.  执行以下内容创建引用NAS文件系统的PVC。

        ```
        cat <<EOF | kubectl apply -f -
        apiVersion: v1
        kind: PersistentVolumeClaim
        metadata:
          name: cnfs-nas-pvc
        spec:
          accessModes:
            - ReadWriteMany
          storageClassName: alibabacloud-nas-cnfs
          resources:
            requests:
              storage: 70Gi
        EOF
        ```

4.  创建应用。

    1.  执行以下内容创建引用PVC的应用。

        ```
        cat <<EOF | kubectl apply -f -
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: cnfs-nas-deployment
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
                  image: nginx:1.14.2
                  ports:
                    - containerPort: 80
                  volumeMounts:
                    - name: cnfs-nas-pvc
                      mountPath: "/data"
              volumes:
                - name: cnfs-nas-pvc
                  persistentVolumeClaim:
                    claimName: cnfs-nas-pvc
        EOF
        ```

    2.  执行以下命令查看所创建应用的工作状态。

        ```
        kubectl get pod
        ```

        预期输出：

        ```
        NAME                             READY   STATUS    RESTARTS   AGE
        cnfs-nas-deployment-86959b****   1/1     Running   0          2s
        ```

        从预期输出可得，所创建的Deployment为Running状态，表示CNFS在该Deployment中使用成功。


## 方式三：使用已有的NAS文件系统创建CNFS

使用CNFS配置已有的NAS文件系统，并使用NAS静态存储卷或NAS动态存储卷挂载已有的NAS文件系统，然后将创建的NAS静态存储卷或NAS动态存储卷绑定到工作负载Deployment中。

1.  使用已有的NAS文件系统创建CNFS。

    1.  执行以下内容使用已有的NAS文件系统创建CNFS。

        ```
        # 当NAS文件系统存在时，加载已创建的NAS文件系统
        cat <<EOF | kubectl apply -f -
        apiVersion: storage.alibabacloud.com/v1beta1
        kind: ContainerNetworkFileSystem
        metadata:
          name: cnfs-nas-filesystem
        spec:
          description: "cnfs"
          type: nas
          reclaimPolicy: Retain
          parameters:
            server: 17f7e4****-hlm35.cn-beijing.nas.aliyuncs.com
        EOF
        ```

        |参数|说明|
        |--|--|
        |description|当前文件系统的简单描述。|
        |type|需要创建的存储类型。|
        |reclaimPolicy|回收策略，目前仅支持Retain策略，删除CNFS时并不会删除NAS文件系统。|
        |parameters.server|NAS的挂载点URL地址。|

    2.  执行以下命令查看NAS文件系统。

        ```
        kubectl get cnfs
        ```

        预期输出：

        ```
        NAME                  AGE
        cnfs-nas-filesystem   6d
        ```

    3.  执行以下命令查看NAS文件系统的详细信息。

        ```
        kubectl get cnfs/cnfs-nas-filesystem -o yaml
        ```

        预期输出：

        ```
        apiVersion: storage.alibabacloud.com/v1beta1
        kind: ContainerNetworkFileSystem
        metadata:
          annotations:
            kubectl.kubernetes.io/last-applied-configuration: |
              {"apiVersion":"storage.alibabacloud.com/v1beta1","kind":"ContainerNetworkFileSystem","metadata":{"annotations":{},"name":"nas-load-mount-target"},"spec":{"description":"filesystem4","parameters":{"filesystemId":"17f7e4****","server":"17f7e48ece-h****.cn-beijing.nas.aliyuncs.com"},"reclaimPolicy":"Retain","type":"nas"}}
          creationTimestamp: "2021-05-14T08:20:09Z"
          finalizers:
          - protection.alibabacloud.com/cnfs
          generation: 6
          name: cnfs-nas-filesystem
          resourceVersion: "122342382"
          selfLink: /apis/storage.alibabacloud.com/v1beta1/containernetworkfilesystems/nas-load-mount-target
          uid: a9e9650c-68b2-405b-8274-0f5b6063****
        spec:
          description: cnfs
          parameters:
            server: 17f7e48ece-h****.cn-beijing.nas.aliyuncs.com
          reclaimPolicy: Retain
          type: nas
        status:
          conditions:
          - lastProbeTime: "2021-05-14 16:20:15"
            reason: The nas filesystem and mount target complete initialization.
            status: Ready
          fsAttributes:
            accessGroupName: DEFAULT_VPC_GROUP_NAME
            encryptType: None
            enableTrashCan: true
            filesystemId: 17f7e4****
            filesystemType: standard
            protocolType: NFS
            regionId: cn-beijing
            server: 17f7e48ece-h****.cn-beijing.nas.aliyuncs.com
            storageType: Capacity
            vSwitchId: vsw-2ze9l3ppwzg6bl02j****
            vpcId: vpc-2ze9sgmehjvwv5x74****
            zoneId: cn-beijing-h
          status: Available
        ```

2.  在NAS存储卷中应用CNFS。具体操作，请参见[方式二：使用CNFS创建自定义NAS文件系统](#section_16b_q45_hx9)的步骤[2](#step_ryf_pnx_irk)到步骤[4](#step_2u2_asy_2oc)。


关于如何监控节点侧NAS存储资源，请参见[NAS存储监控使用示例](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/容器存储监控/使用csi-plugin组件监控节点侧存储资源.md)。

