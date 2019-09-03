# 静态OSS卷 {#task_2022046 .task}

本文为您介绍如何使用OSS静态存储卷。

您已经创建静态OSS存储卷，请参见[创建存储空间](../../../../cn.zh-CN/快速入门/创建存储空间.md#)。

**说明：** 相同region的OSS Bucket 可以使用内网地址进行挂载。

## 创建Secret {#section_obo_kuo_v5v .section}

以下为通过Secret来配置用户的AccessKey信息的Yaml示例文件：

``` {#codeblock_tx9_nsm_b2r}
apiVersion: v1
kind: Secret
metadata:
  name: oss-secret
  namespace: default
stringData:
  akId: ***
  akSecret: ***
```

**说明：** 创建Secret选择的namespace需要和应用所在namespaces一致。

`akId`、`akSecret`需要替换成用户的AccessKeyID和AccessKeySecret。

## 创建静态卷PV/PVC {#section_arx_7ar_3ew .section}

以下为创建静态卷PV/PVC的yaml示例文件：

``` {#codeblock_f3s_5oe_tb0}
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: oss-pvc
spec:
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storage: 5Gi
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: oss-csi-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  csi:
    driver: ossplugin.csi.alibabacloud.com
    volumeHandle: data-id
    nodePublishSecretRef:
      name: oss-secret
      namespace: default
    volumeAttributes:
      bucket: "oss"
      url: "oss-cn-hangzhou.aliyuncs.com"
      otherOpts: "-o max_stat_cache_size=0 -o allow_other"
```

-   `nodePublishSecretRef`：定义挂载PV时通过Secret对象来获取AccessKey信息。
-   `volumeHandle`：配置PV的名称。
-   `bucket`： 需要挂载的OSS Bucket。
-   `url`：挂载 OSS 的接入域名，挂载节点和Bucket相同region时，可使用内网地址。
-   `otherOpts`：挂载 OSS 时支持输入定制化参数，格式为：`-o *** -o ***`。

## 配置PV/PVC的AccessKey {#section_de7_06v_8wy .section}

[创建静态卷PV/PVC](#section_arx_7ar_3ew)是通过Secret为plugin提供AccessKey信息，您也可以直接在PV中配置AccessKey信息。示例Yaml文件如下：

``` {#codeblock_wf0_maj_nmp}
apiVersion: v1
kind: PersistentVolume
metadata:
  name: oss-csi-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  csi:
    driver: ossplugin.csi.alibabacloud.com
    volumeHandle: data-id
    volumeAttributes:
      bucket: "oss"
      url: "oss-cn-hangzhou.aliyuncs.com"
      otherOpts: "-o max_stat_cache_size=0 -o allow_other"
      akId: "***"
      akSecret: "***"
```

## 创建应用 {#section_u8z_t6e_5uj .section}

``` {#codeblock_45v_5uk_nd3}
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
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
          - name: oss-pvc
            mountPath: "/data"
      volumes:
        - name: oss-pvc
          persistentVolumeClaim:
            claimName: oss-pvc
```

