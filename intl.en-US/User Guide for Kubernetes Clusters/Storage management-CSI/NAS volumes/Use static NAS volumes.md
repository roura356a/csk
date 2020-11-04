# Use static NAS volumes

This topic describes how to use a static Alibaba Cloud Network Attached Storage \(NAS\) volume by creating a persistent volume \(PV\) and a persistent volume claim \(PVC\).

-   A static NAS volume is created. For more information, see [Manage file systems]().
-   A NAS mount point is created. For more information, see [Manage mount targets]().

    The NAS mount point must be in the same Virtual Private Cloud \(VPC\) as the cluster nodes.


## Create a PV and a PVC

The following sample YAML file shows how to create a PV and a PVC:

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

**Note:**

-   `driver`: the driver type. In this example, set this parameter to nasplugin.csi.alibabacloud.com, indicating that the Alibaba Cloud NAS CSI plug-in is used.
-   `volumeHandle`: the PV name.
-   `host`: the NAS mount point.
-   `path`: the sub-directory to attach.
-   `vers`: the target NAS version.
-   `mode`: the permissions on the attached directory.

## Create an application

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

