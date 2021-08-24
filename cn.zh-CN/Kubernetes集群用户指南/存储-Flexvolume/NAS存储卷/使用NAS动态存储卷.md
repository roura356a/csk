---
keyword: [动态存储卷, NAS, Flexvolume]
---

# 使用NAS动态存储卷

本方案实现的动态NAS卷，是在某个NAS文件系统下通过创建子目录并把子目录映射为一个动态PV提供给应用。

-   已创建Kubernetes集群，并且在该集群中安装Flexvolume插件。具体操作，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   已部署alicloud-nas-controller组件。具体操作，请参见[安装与升级Flexvolume组件](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/安装与升级Flexvolume组件.md)。

## 创建动态NAS卷

1.  配置StorageClass。

    配置示例如下所示：

    ```
    apiVersion: storage.k8s.io/v1
    kind: StorageClass
    metadata:
      name: alicloud-nas
    mountOptions:
    - nolock,tcp,noresvport
    - vers=3
    parameters:
      server: "23a9649583-i****.cn-shenzhen.nas.aliyuncs.com:/nasroot1/"
      driver: flexvolume
    provisioner: alicloud/nas
    reclaimPolicy: Delete
    ```

    **说明：**

    -   mountOptions：表示生成的PV Options配置，挂载NAS卷时使用这个Options挂载。
    -   server：表示生成目标PV所使用NAS挂载点列表。格式为 nfsurl1:/path1,nfsurl2:/path2；当配置多个Server时，通过此StorageClass创建的PV会轮询使用上述Server作为配置参数；极速NAS配置路径需要以/share开头。
    -   driver：支持Flexvolume、NFS两种驱动，默认为NFS。
    -   reclaimPolicy：PV的回收策略，建议配置为Retain。
        -   当配置为Delete时，删除PV后NAS文件系统中的对应目录会默认修改名字（例如，path-name会被修改为archived-path-name）。
        -   如果需要删除文件系统中对应的存储目录，可在StorageClass中配置`archiveOnDelete`为false。
2.  使用动态卷。

    使用以下示例创建Service及StatefulSet应用：

    ```
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      ports:
      - port: 80
        name: web
      clusterIP: None
      selector:
        app: nginx
    ---
    apiVersion: apps/v1
    kind: StatefulSet
    metadata:
      name: web
    spec:
      selector:
        matchLabels:
          app: nginx
      serviceName: "nginx"
      replicas: 5
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
            - mountPath: "/data"
              name: html
    ```


