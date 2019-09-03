# 静态NAS卷 {#task_2022002 .task}

您可以通过 PV/PVC 方式使用阿里云 NAS 数据卷。

-   您已经创建一个静态NAS卷。请参见[管理文件系统](../../../../cn.zh-CN/控制台用户指南/管理文件系统.md#)。
-   您已经创建NAS挂载点。请参见[管理挂载点](../../../../cn.zh-CN/控制台用户指南/管理挂载点.md#)。

    NAS挂载点需要和集群节点在同一个vpc内。


## 创建静态PV/PVC {#section_wb8_puo_yif .section}

以下为创建静态卷PV/PVC的yaml示例文件：

``` {#codeblock_dta_a3d_2wm}
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
      vers: "3"
      options: "noresvport,nolock"
---
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

**说明：** 

-   `driver`：驱动类型。本例中取值为nasplugin.csi.alibabacloud.com，表示使用阿里云NAS CSI插件。
-   `volumeHandle`：配置PV的名称。
-   `host`：NAS挂载点。
-   `path`：挂载子目录。
-   `vers`：NAS目标版本。
-   `mode`：挂载目录权限。

## 创建应用 {#section_9p8_6m5_uh4 .section}

``` {#codeblock_q85_u61_p34}
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
          - name: nas-pvc
            mountPath: "/data"
      volumes:
        - name: nas-pvc
          persistentVolumeClaim:
            claimName: nas-pvc
```

