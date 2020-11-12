# HostPath数据卷

HostPath卷能将主机节点文件系统上的文件或目录挂载到您的Pod中。本文介绍如何使用HostPath数据卷。

阿里云ASK集群兼容社区HostPath本地挂载方案的详细信息，请参见[hostpath](https://kubernetes.io/zh/docs/concepts/storage/volumes/#hostpath)。

## HostPath数据卷使用示例

-   Pod中直接定义hostPath的方式使用HostPath数据卷的示例模板如下。

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

-   通过PV和PVC定义hostPath使用HostPath数据卷的示例模板如下。

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

-   Pod运行时挂载HostPath数据卷。

    Kubernetes生态中，Pod一旦运行，其挂载的数据卷就不可以修改，所以原则上运行中的容器不能动态挂载数据卷。下面方式可以在一定程度上实现运行中Pod动态挂载外部存储的需求。

    **说明：**

    -   本方案依赖Linux中mountPropagation的Bidirectional属性，可以将主机挂载目录映射到容器中。
    -   目标Pod必须具有privilege权限（Bidirectional需求）。
    -   目标Pod需要挂载一个主机目录，并配置Bidirectional，运行中的容器靠这个目录接收外部挂载。
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
    上述配置，可以在Pod运行的时候，在Pod所在节点上在/mnt/dynamic-volume/\*\*\*\*挂载外部存储，从而将外部存储卷映射到Pod内部，实现了运行中Pod动态挂载外部存储的能力。


