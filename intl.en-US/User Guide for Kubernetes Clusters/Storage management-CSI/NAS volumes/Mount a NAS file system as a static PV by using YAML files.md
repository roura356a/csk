# Mount a NAS file system as a static PV by using YAML files

This topic describes how to mount a Network Attached Storage \(NAS\) file system as a static persistent volume \(PV\) by using a YAML file.

-   An NAS file system is created. For more information, see [Manage file systems]().
-   A mount target is created for the NAS file system. For more information, see [Manage mount targets]().

    The mount target and the cluster node where the NAS file system is mounted are deployed in the same virtual private cloud \(VPC\).


## Step 1: Create a static PV

The following YAML template is an example of how to create a static PV:

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

**Note:**

-   `driver`: The driver that is used. In this example, set this parameter to `nasplugin.csi.alibabacloud.com`. This indicates that the Alibaba Cloud NAS CSI plug-in is used.
-   `volumeHandle`: the name of the PV.
-   `server`: the address of the mount target.
-   `path`: the subdirectory of the NAS file system that is mounted. If you mount an Extreme NAS file system, the path must start with /share.
-   `vers`: the version of the NFS protocol. We recommend that you use NFSv3. Extreme NAS file systems support only NFSv3.

## Step 2: Create a static PVC

The following YAML template is an example of how to create a static persistent volume claim \(PVC\):

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

## Step 3: Create an application

The following YAML template is an example of how to create an application:

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

You can also mount an NAS file system as a static PV in the console. For more information, see [Use a static NAS volume in the ACK console](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/NAS volumes/Use a static NAS volume in the ACK console.md).

