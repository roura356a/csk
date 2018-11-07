# 使用阿里云 NAS {#concept_jhy_3ys_vdb .concept}

您可以在容器服务 Kubernetes 集群中使用阿里云 NAS 数据卷。

目前阿里云 NAS 提供两种 Kubernetes 挂载方式：

-   [静态存储卷](#section_hx3_gzs_vdb)

    其中，静态存储卷又支持下面两种使用方式：

    -   通过 flexvolume 插件使用
        -   通过 volume 方式使用
        -   使用 PV/PV
    -   通过 Kubernetes 的 NFS 驱动使用
-   [动态存储卷](#section_iz3_gzs_vdb)

## 前提条件 {#section_ll4_lzs_vdb .section}

使用 NAS 数据卷之前，您需要先在 NAS 管理控制台上创建文件系统，并在文件系统中添加 Kubernetes 集群的挂载点。创建的 NAS 文件系统需要和您的集群位于同一 VPC。

## 静态存储卷 {#section_hx3_gzs_vdb .section}

您可以通过阿里云提供的 flexvolume 插件使用阿里云 NAS 文件存储服务或者通过 Kubernetes 的 NFS 驱动使用阿里云 NAS 文件存储服务。

**通过 flexvolume 插件使用**

使用 flexvolume 插件，您可以通过 volume 方式使用阿里云 NAS 数据卷或者通过 PV/PVC 方式使用阿里云 NAS 数据卷。

**说明：** 

-   NAS 为共享存储，可以同时为多个 Pod 提供共享存储服务。
-   server：为 NAS 数据盘的挂载点。
-   path：为连接 NAS 数据卷的挂载目录，支持挂载 NAS 子目录，且当子目录不存在时，会自动创建子目录并挂载。
-   vers：定义 nfs 挂载协议的版本号，支持3.0 和 4.0。
-   mode：定义挂载目录的访问权限，注意挂载 NAS 盘根目录时不能配置挂载权限。当 NAS 盘中数据量很大时，配置 mode 会导致执行挂载非常慢，甚至挂载失败。

**通过 volume 方式使用**

使用 `nas-deploy.yaml` 文件创建 Pod。

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

**使用 PV/PVC**

**步骤 1 创建 PV**

您可以使用 yaml 文件或者通过阿里云容器服务控制台界面创建 NAS 数据卷。

-   使用 yaml 文件创建 PV

    使用 `nas-pv.yaml` 文件创建 PV。

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

-   通过控制台界面创建 NAS 数据卷
    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
    2.  在 Kubernetes 菜单下，单击左侧导航栏中的**集群** \> **存储**，进入数据卷列表页面。
    3.  选择所需的集群，单击页面右上角的**创建**。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/18764/154159139610390_zh-CN.png)

    4.  在创建数据卷对话框中，配置数据卷的相关参数。

        -   **数据卷类型**：本示例中为NAS。
        -   **数据卷名**：创建的数据卷的名称。数据卷名在集群内必须唯一。本例为 pv-nas。
        -   **数据卷总量**：所创建数据卷的容量。注意不能超过磁盘容量。
        -   **访问模式**：默认为 ReadWriteMany。
        -   **挂载点域名**：集群在 NAS 文件系统中挂载点的挂载地址。
        -   **子目录**：NAS 路径下的子目录，以/开头，设定后数据卷将挂载到指定的子目录。
            -   如果 NAS 根目录下没有此子目录，会默认创建后再挂载。
            -   您可以不填此项，默认挂载到 NAS 根目录。
        -   **权限**：设置挂载目录的访问权限，例如：755、644、777 等。
            -   只有挂载到 NAS 子目录时才能设置权限，挂载到根目录时不能设置。
            -   您可以不填此项，默认权限为 NAS 文件原来的权限。
        -   **标签**：为该数据卷添加标签。
        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/18764/154159139610391_zh-CN.png)

    5.  完成配置后，单击**创建**。

**步骤 2 创建 PVC**

使用 nas-pvc.yaml 文件创建 PVC。

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

**步骤 3 创建 Pod**

使用nas-pod.yaml文件创建 pod。

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

**通过 Kubernetes 的 NFS 驱动使用**

**步骤 1 创建 NAS 文件系统**

登录 [文件存储管理控制台](https://nas.console.aliyun.com/)，创建一个 NAS 文件系统。

**说明：** 创建的 NAS 文件系统需要和您的集群位于同一地域。

假设您的挂载点为 `055f84ad83-ixxxx.cn-hangzhou.nas.aliyuncs.com`。

**步骤 2 创建 PV**

您可以使用编排模板或者通过阿里云容器服务控制台界面创建 NAS 数据卷。

-   **使用编排模板**

    使用nas-pv.yaml文件创建 PV。

    执行以下命令创建一个类型为 NAS 的 PesistentVolume。

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

-   **通过控制台界面创建 NAS 数据卷**

    具体操作参见 [使用PV/PVC](#ol_yx3_gzs_vdb) 中关于通过控制台界面创建 NAS 数据卷的内容。


**步骤 2 创建 PVC**

创建一个 PersistentVolumeClaim 来请求绑定该 PersistentVolume。

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

**步骤 3 创建 Pod**

创建一个应用来申明挂载使用该数据卷。

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

至此，您就将 NAS 远程文件系统挂载到了您的 Pod 应用当中了。

## 动态存储卷 {#section_iz3_gzs_vdb .section}

使用动态 NAS 存储卷需要您手动安装驱动插件，并配置 NAS 挂载点。

**说明：** 动态生成NAS存储卷的本质是在一个已有的文件系统上，自动生成一个目录，这个目录定义为目标存储卷。

**安装插件**

```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: alicloud-nas
mountOptions:
- vers=4.0
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
          nfs:
            server: 0cd8b4a576-mmi32.cn-hangzhou.nas.aliyuncs.com
            path: /
```

**使用动态存储卷**

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

