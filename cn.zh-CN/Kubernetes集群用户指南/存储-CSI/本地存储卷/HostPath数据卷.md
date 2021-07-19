---
keyword: [HostPath, 本地存储卷]
---

# HostPath数据卷

HostPath卷能将主机节点文件系统上的文件或目录挂载到您的Pod中。本文介绍如何使用HostPath数据卷。

阿里云ACK集群兼容社区HostPath本地挂载方案的详细信息，请参见[Hostpath](https://kubernetes.io/zh/docs/concepts/storage/volumes/#hostpath)。

## 挂载模式

HostPath支持以下几种挂载模式。

|挂载模式|描述|
|----|--|
|DirectoryOrCreate|如果在给定路径上什么都不存在，将根据需要创建空目录，权限设置为0755，与Kubelet有相同的组和属主信息。|
|Directory|在给定路径上必须存在目录。|
|FileOrCreate|如果在给定路径上什么都不存在，那么将在给定路径根据需要创建空文件，权限设置为0644，具有与Kubelet相同的组和所有权。|
|File|在给定路径上必须存在文件。|

## HostPath数据卷使用示例

-   Pod中以直接定义`hostPath`的方式使用HostPath数据卷：

```
apiVersion: v1
kind: Pod
metadata:
  name: test
spec:
  containers:
  - image: nginx:1.7.9
    name: test
    volumeMounts:
    - mountPath: /test
      name: test-volume
  volumes:
  - name: test-volume
    hostPath:
      path: /data
      type: DirectoryOrCreate
```

-   通过PV和PVC定义`hostPath`的方式使用HostPath数据卷：

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: task-pv-volume
  labels:
    type: local
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/data"
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: hostpath
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
```

-   Pod运行时挂载HostPath数据卷：

    Kubernetes生态中，Pod运行时，其挂载的数据卷不可以修改，所以原则上运行中的容器不能动态挂载数据卷。下面方式可以在一定程度上实现运行中Pod动态挂载外部存储的需求。

    **说明：**

    -   本方案依赖Linux中mountPropagation的Bidirectional属性，可以将主机挂载目录映射到容器中。
    -   目标Pod必须具有Privilege权限（Bidirectional需求）。
    -   目标Pod需要挂载一个主机目录，并配置Bidirectional，运行中的容器依赖该目录接收外部挂载。
    部署一个Nginx应用使用HostPath数据卷的示例模板如下。

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: deployment-nas
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
            command: ["sh", "-c"]
            args: ["sleep 10000"]
            securityContext:
              privileged: true
              capabilities:
                add: ["SYS_ADMIN"]
              allowPrivilegeEscalation: true
            volumeMounts:
              - name: dynamic-volume
                mountPropagation: "Bidirectional"
                mountPath: "/dynamic-volume"
          volumes:
            - name: dynamic-volume
              hostPath:
                path: /mnt/dynamic-volume
                type: DirectoryOrCreate
    ```

    **说明：**

    -   主机目录：/mnt/dynamic-volume。
    -   Pod内目录：/dynamic-volume。
    上述配置，可以在Pod运行时，在Pod所在节点上的/mnt/dynamic-volume/\*\*\*\*路径挂载外部存储，从而将外部存储卷映射到Pod内部，实现了运行中Pod动态挂载外部存储的能力。


