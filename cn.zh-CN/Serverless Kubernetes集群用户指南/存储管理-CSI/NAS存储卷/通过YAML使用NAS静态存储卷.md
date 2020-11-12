# 通过YAML使用NAS静态存储卷

您本文为您介绍如何通过YAML的方式使用阿里云NAS静态存储卷。

-   您已经创建一个静态NAS卷。请参见[管理文件系统]()。
-   您已经创建NAS挂载点。请参见[管理挂载点]()。

    NAS挂载点需要和集群节点在同一个VPC内。


## 步骤一：创建静态PV

以下为创建静态卷PV的YAML示例文件。

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nas
  labels:
    alicloud-pvname: pv-nas
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  csi:
    driver: nasplugin.csi.alibabacloud.com
    volumeHandle: pv-nas
    volumeAttributes:
      server: "2564f49129-ysu87.cn-shenzhen.nas.aliyuncs.com"
      path: "/csi"
  mountOptions:
  - nolock,tcp,noresvport
  - vers=3
```

**说明：**

-   `driver`：驱动类型。本例中取值为`nasplugin.csi.alibabacloud.com`，表示使用阿里云NAS CSI插件。
-   `volumeHandle`：配置PV的名称。
-   `server`：NAS挂载点。
-   `path`：挂载子目录，极速NAS需要以/share开头。
-   `vers`：挂载NAS数据卷的NFS协议版本号，推荐使用v3；极速类型NAS只支持v3。

## 步骤二：创建静态PVC

以下为创建静态卷PVC的YAML示例文件。

```
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-nas
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 5Gi
  selector:
    matchLabels:
      alicloud-pvname: pv-nas
```

## 步骤三：创建应用

以下为创建应用的YAML示例文件。

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-nas
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
          - name: pv-nas
            mountPath: "/data"
      volumes:
        - name: pv-nas
          persistentVolumeClaim:
            claimName: pvc-nas
```

您也可以通过控制台的方式使用NAS静态存储卷，请参见[通过控制台使用NAS静态存储卷](/cn.zh-CN/Kubernetes集群用户指南/存储管理-CSI/NAS存储卷/通过控制台使用NAS静态存储卷.md)。

