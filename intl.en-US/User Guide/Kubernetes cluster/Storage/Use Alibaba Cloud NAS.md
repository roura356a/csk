# Use Alibaba Cloud NAS {#concept_jhy_3ys_vdb .concept}

You can use the Alibaba Cloud NAS data volumes in Container Service Kubernetes clusters.

Currently, Alibaba Cloud NAS provides the following two Kubernetes mount methods:

-   [Static storage volumes](#section_hx3_gzs_vdb)

    You can use the static storage volumes by:

    -   Using the flexvolume plug-in.
        -   Using the volume method.
        -   Using PV/PVC.
    -   Using NFS drive of Kubernetes.
-   [Dynamic storage volumes](#section_iz3_gzs_vdb)

## Prerequisites {#section_ll4_lzs_vdb .section}

Before using NAS data volumes, you must create a file system in the NAS console and add the mount point of a Kubernetes cluster in the file system. The created NAS file system and your cluster must be in the same Virtual Private Cloud \(VPC\).

## Static storage volumes {#section_hx3_gzs_vdb .section}

You can use Alibaba Cloud NAS file storage service by using the flexvolume plug-in provided by Alibaba Cloud or the NFS drive of Kubernetes.

**Use flexvolume plug-in**

Use the flexvolume plug-in and then you can use the Alibaba Cloud NAS data volumes by using the volume method or using PV/PVC.

**Note:** 

-   NAS is a shared storage and can provide shared storage service for multiple pods at the same time.
-   server: The mount point of the NAS data disk.
-   path: The mount directory for connecting to the NAS data volumes. You can mount NAS data volumes to a NAS sub-directory. The system automatically creates the sub-directory if the sub-directory does not exist and mounts the NAS data volumes to the created sub-directory.
-   vers: Defines the version number of NFS mount protocol. 3.0 and 4.0 are supported.
-   mode: Defines the access permission of the mount directory. The mount permission cannot be configured if you mount the NAS data volumes to the NAS root directory. If the NAS disk contains a huge amount of data, configuring the mode leads to the slow mounting or even the mounting failure.

**Using the volume method.**

Use the `nas-deploy.yaml` file to create the pod.

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
          vers: "4.0"
```

**Using PV/PVC.**

**Step 1 Create PV**

You can create NAS data volumes in the Container Service console or by using the YAML file.

-   Create PV by using YAML file

    Use the `nas-pv.yaml` file to create the PV.

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
          vers: "4.0"
    ```

-   Create NAS data volumes in Container Service console
    1.  Log on to the [Container Service console](https://cs.console.aliyun.com).
    2.  Under Kubernetes, click **Clusters** \> **Volumes** in the left-side navigation pane.
    3.  Select the cluster from the Clusters drop-down list and then click **Create** in the upper-right corner.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/18764/153820336810390_en-US.png)

    4.  The Create Data Volume dialog box appears. Configure the data volume parameters.

        -   **Type:** Select NAS in this example.
        -   **Name**: Enter the name of the data volume you are about to create. The data volume name must be unique in the cluster. In this example, enter pv-nas.
        -   **Capacity**: Enter the capacity of the data volume to be created. Make sure the capacity cannot exceed the disk capacity.
        -   **Access Mode**: ReadWriteMany is selected by default.
        -   **Mount Point Domain Name**: Enter the mount address of the mount point in the NAS file system for the cluster.
        -   **Path**: The sub-directory under the NAS path, which starts with a forward slash/.The data volume is mounted to the specified sub-directory after being created.
            -   If this sub-directory does not exist in the NAS root directory, the data volume is mounted after the sub-directory is created by default.
            -   If this field is left empty, the data volume is mounted to the NAS root directory by default.
        -   **Privilege**: Configure the access permission of the mount directory, such as 755, 644, and 777.
            -   You can only configure the privilege when the data volume is mounted to the NAS sub-directory, that is, you cannot configure the privilege if the data volume is mounted to the NAS root directory.
            -   If this field is left empty, use the permissions of the NAS files by default.
        -   **Tag**: Click Add Tag to add tags for this data volume.
        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/18764/153820336810391_en-US.png)

    5.  Click **Create** after the configurations.

**Step 2. Create PVC**

Use the nas-pvc.yaml file to create the PVC

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

**Step 3 Create pod**

Use the nas-pod.yaml file to create the pod.

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

**Using NFS drive of Kubernetes.**

**Step 1 Create a NAS file system**

Log on to the [NAS console](https://nas.console.aliyun.com/) to create a NAS file system.

**Note:** The created NAS file system and your cluster must be in the same region.

Assume that your mount point is`055f84ad83-ixxxx.cn-hangzhou.nas.aliyuncs.com`。

**Step 2 Create PVC**

You can create NAS data volumes in the Container Service console or by using an orchestration template.

-   **Use an orchestration template**

    Use the nas-pv.yaml file to create the PV.

    Run the following commands to create a NAS type PersistentVolume.

    ```
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
       persistentVolumeReclaimPolicy: Retain
       nfs:
         path: /
         server: 055f84ad83-ixxxx.cn-hangzhou.nas.aliyuncs.com
    EOF
    ```

-   **Create NAS data volumes on Container Service console**

    For more information, see Create NAS data volumes on Container Service console in [Use PV/PVC](#ol_yx3_gzs_vdb).


**Step 2 Create PVC**

Create a PersistentVolumeClaim to request to bind this PersistentVolume.

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

**Step 3 Create pod**

Create an application to declare to mount and use this data volume.

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

Then, the NAS remote file system is mounted to your pod application.

## Dynamic storage volumes {#section_iz3_gzs_vdb .section}

To use dynamic NAS storage volumes, you must manually install the drive plug-in and configure the NAS mount point.

**Install the plug-in**

```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: alicloud-nas
provisioner: alicloud/nas
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: alicloud-nas-controller
  namespace: kube-system
---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: run-alicloud-nas-controller
subjects:
  - kind: ServiceAccount
    name: alicloud-nas-controller
    namespace: kube-system
roleRef:
  kind: ClusterRole
  name: alicloud-disk-controller-runner
  apiGroup: rbac.authorization.k8s.io
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
      serviceAccount: alicloud-nas-controller
      containers:
        - name: alicloud-nas-controller
          image: registry.cn-hangzhou.aliyuncs.com/acs/alicloud-nas-controller:v1.8.4
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
          nfs:
            server: 0cd8b4a576-mmi32.cn-hangzhou.nas.aliyuncs.com
            path: /
```

**Use dynamic storage volumes**

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

