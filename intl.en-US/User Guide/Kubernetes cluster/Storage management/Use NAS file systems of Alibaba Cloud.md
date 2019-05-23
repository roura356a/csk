# Use NAS file systems of Alibaba Cloud {#concept_jhy_3ys_vdb .concept}

You can use Alibaba Cloud NAS volumes in a Kubernetes cluster of Container Service.

You can mount a NAS file system of Alibaba Cloud to a Kubernetes cluster as either of the following two types of volumes:

-   [Static volumes](#section_hx3_gzs_vdb) 

    You can use a static volume in either of the following two ways:

    -   Use a static volume through the flexvolume plugin.
        -   Use a static volume directly.
        -   Use a static volume through a Persistent Volume \(PV\) and a Persistent Volume Claim \(PVC\).
    -   Use a static volume through the NFS driver of Kubernetes.
-   [Dynamic volumes](#section_iz3_gzs_vdb)

## Prerequisites {#section_ll4_lzs_vdb .section}

You have created a NAS file system in the NAS console and added a mount point for a Kubernetes cluster in the file system. You must make sure that the NAS file system and your cluster are in the same VPC.

## Static volumes {#section_hx3_gzs_vdb .section}

You can use the Alibaba Cloud NAS file storage service by using the flexvolume plugin provided by Alibaba Cloud or the NFS driver of Kubernetes.

**Use a static volume through the flexvolume plugin**

With a flexvolume plugin, you can use an Alibaba Cloud NAS volume directly or through a PV and a PVC.

**Note:** 

-   NAS: a shared storage system that can provide storage services for multiple pods at the same time.
-   server: defines the mount point of a NAS file system.
-   path: defines the mount directory that connects to the NAS volume. You can specify a NAS sub-directory and mount it to your NAS volume. If the NAS sub-directory specified by you does not exist, the system automatically creates the NAS sub-directory and mounts it to your NAS volume.
-   vers: defines the version number of the NFS mount protocol. NFS file system versions 3.0 and 4.0 are supported.
-   mode:defines the access permission to a mount directory. When the mount directory is the root directory of a NAS file system, the access permission to the root directory cannot be set. If you set the mode parameter for a NAS file system that stores a large amount of data, the process of mounting the NAS file system to a cluster may take an excessive amount of time or even fail.

**Use a static volume directly**

Use a `nas-deploy.yaml` file to create a pod as follows:

```
apiVersion: v1
kind: Pod
metadata:
  name: "flexvolume-nas-example"
spec:
  containers:
    - name: "nginx"
      image: "nginx"
      volumeMounts:
        - name: "nas1"
          mountPath: "/data"
  volumes:
    - name: "nas1"
      flexVolume:
        driver: "alicloud/nas"
        options:
          server: "0cd8b4a576-grs79.cn-hangzhou.nas.aliyuncs.com"
          path: "/k8s"
          vers: "3"
```

**Use a static volume through a PV and a PVC**

**Step 1: Create a PV**

You can create a NAS volume by using a YAML file or create a NAS volume in the Alibaba Cloud Container Service console.

-   Create a PV by using a YAML file.

    Use a `nas-pv.yaml` file to create a PV as follows:

    ```
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: pv-nas
    spec:
      capacity:
        storage: 5Gi
      storageClassName: nas
      accessModes:
        - ReadWriteMany
      flexVolume:
        driver: "alicloud/nas"
        options:
          server: "0cd8b4a576-uih75.cn-hangzhou.nas.aliyuncs.com"
          path: "/k8s"
          vers: "3"
    ```

-   Create a NAS volume in the Container Service console.
    1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
    2.  In the left-side navigation pane under Kubernetes, choose **Clusters** \> **Volumes**.
    3.  Select the target cluster from the cluster drop-down list and then click **Create** in the upper-right corner.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/18764/155859180810390_en-US.png)

    4.  In the displayed dialog box, set the volume parameters.

        -   **Storage type**: NAS is selected in this example.
        -   **Name**: Customize a volume name. The volume name must be unique in the cluster. In this example, pv-nas is set as the volume name.
        -   **Capacity**: Set the volume capacity. Make sure that the volume capacity does not exceed the NAS file system capacity.
        -   **Access Mode**: By default, it is set to ReadWriteOnce.
        -   **Mount Point Domain Name**: Enter the mount address of the mount point that is used to mount the NAS file system to the Kubernetes cluster.
        -   **Path**: sub-directory under the NAS path, which starts with a forward slash \(/\). If you specify a sub-directory, your volume will be mounted to the sub-directory.
            -   If no sub-directory exists in the root directory of a NAS file system, the system automatically creates a sub-directory by default.
            -   This parameter is optional. A NAS volume is mounted to the root directory of a NAS file system by default.
        -   **Privilege**: Set the access permission to the mount directory. For example, you can set this parameter to 755, 644, or 777.
            -   You can set this parameter only if you mount a NAS volume to the NAS sub-directory. This parameter cannot be set if you mount a NAS volume to the NAS root directory.
            -   This parameter is optional. By default, the original access permission to a NAS file system is used.
        -   **Tag**: Add tags to the volume.
        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/18764/155859180810391_en-US.png)

    5.  Click **Create**.

**Step 2: Create a PVC**

Use a nas-pvc.yaml file to create a PVC as follows:

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nas
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: nas
  resources:
    requests:
      storage: 5Gi
```

**Step 3: Create a pod**

Use a nas-pod.yaml file to create a pod as follows:

```
apiVersion: v1
kind: Pod
metadata:
  name: "flexvolume-nas-example"
spec:
  containers:
    - name: "nginx"
      image: "nginx"
      volumeMounts:
          - name: pvc-nas
            mountPath: "/data"
  volumes:
  - name: pvc-nas
    persistentVolumeClaim:
        claimName: pvc-nas
```

 **Use the Kubernetes NFS driver** 

**Note:** Alibaba Cloud NAS supports NFS 3.0 and NFS 4.0. You must specify a valid NFS version when you create a NAS volume.

**Step 1: Create a NAS file system**

Log on to the [NAS console](https://partners-intl.console.aliyun.com/#/nas) to create a NAS file system.

**Note:** You must ensure that the NAS file system and your cluster are in the same region.

For example, assume that the mount point of your NAS file system is `055f84ad83-ixxxx.cn-hangzhou.nas.aliyuncs.com`.

**Step 2: Create a PV**

You can create a NAS volume by using an orchestration template or the Alibaba Cloud Container Service console.

-   **Use an orchestration template to create a NAS volume** 

    Use a nas-pv.yaml file to create a PV.

    Run the following command to create a NAS PV:

    ``` {#codeblock_u8a_3bp_8oe}
    root@master # cat << EOF |kubectl apply -f -
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: nas
    spec:
      capacity:
        storage: 8Gi
      accessModes:
        - ReadWriteMany
      mountOptions:
        - noresvport
        - nfsvers=3
      persistentVolumeReclaimPolicy: Retain
      nfs:
        path: /
        server: 055f84ad83-ixxxx.cn-hangzhou.nas.aliyuncs.com
    EOF
    ```

-   **Create a NAS volume in the Container Service console** 

    For more information, see [Use a PV and a PVC](#ol_yx3_gzs_vdb).


**Step 2: Create a PVC**

Create a PVC to request to bind the PV.

```
root@master # cat << EOF | kubectl apply -f -
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
     name: nasclaim
spec:
     accessModes:
       - ReadWriteMany
     resources:
       requests:
         storage: 8Gi
EOF
```

**Step 3: Create a pod**

Create an application to declare to mount and use the volume.

```
root@master # cat << EOF |kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
     name: mypod
spec:
     containers:
       - name: myfrontend
         image: registry.aliyuncs.com/spacexnice/netdia:latest
         volumeMounts:
         - mountPath: "/var/www/html"
           name: mypd
     volumes:
       - name: mypd
         persistentVolumeClaim:
           claimName: nasclaim
EOF
```

The NAS file system is successfully mounted to the application that runs on the pod.

## Dynamic volumes {#section_iz3_gzs_vdb .section}

To use a dynamic NAS volume, you need to manually install a driver plugin and configure a NAS mount point.

**Note:** To dynamically generate a NAS volume is to automatically generate a directory in an existing NAS file system. This directory is defined as the target volume.

**Install a plugin** 

``` {#codeblock_4tg_7jz_mnz}
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: alicloud-nas
mountOptions:
- vers=3
provisioner: alicloud/nas
reclaimPolicy: Retain

---
kind: Deployment
apiVersion: extensions/v1beta1
metadata:
  name: alicloud-nas-controller
  namespace: kube-system
spec:
  replicas: 1
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: alicloud-nas-controller
    spec:
      tolerations:
      - effect: NoSchedule
        operator: Exists
        key: node-role.kubernetes.io/master
      - effect: NoSchedule
        operator: Exists
        key: node.cloudprovider.kubernetes.io/uninitialized
      nodeSelector:
        node-role.kubernetes.io/master: ""
      serviceAccount: admin
      containers:
        - name: alicloud-nas-controller
          image: registry.cn-hangzhou.aliyuncs.com/acs/alicloud-nas-controller:v3.1.0-k8s1.11
          volumeMounts:
          - mountPath: /persistentvolumes
            name: nfs-client-root
          env:
            - name: PROVISIONER_NAME
              value: alicloud/nas
            - name: NFS_SERVER
              value: 0cd8b4a576-mmi32.cn-hangzhou.nas.aliyuncs.com
            - name: NFS_PATH
              value: /
      volumes:
      - name: nfs-client-root
        flexVolume:
          driver: alicloud/nas
          options:
            path: /
            server: 0cd8b4a576-mmi32.cn-hangzhou.nas.aliyuncs.com
            vers: "3"
```

**Use the dynamic volume**

```
apiVersion: apps/v1beta1
kind: StatefulSet
metadata:
  name: web
spec:
  serviceName: "nginx"
  replicas: 2
  volumeClaimTemplates:
  - metadata:
      name: html
    spec:
      accessModes:
        - ReadWriteOnce
      storageClassName: alicloud-nas
      resources:
        requests:
          storage: 2Gi
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:alpine
        volumeMounts:
        - mountPath: "/usr/share/nginx/html/"
          name: html
```

