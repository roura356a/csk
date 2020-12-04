# NAS动态存储卷

本方案实现的动态NAS卷，是在某个NAS文件系统下通过创建子目录并把子目录映射为一个动态PV提供给应用。

使用此方案，需要在集群中部署Flexvolume驱动（Kubernetes集群默认已部署该驱动）。

您已经部署了alicloud-nas-controller。请参见[安装插件](/intl.zh-CN/Kubernetes集群用户指南/存储管理-Flexvolume/安装插件.md)。

## 创建动态NAS卷

1.  配置StorageClass。

    配置示例如下：

    ```
    apiVersion: storage.k8s.io/v1
    kind: StorageClass
    metadata:
      name: alicloud-nas
    mountOptions:
    - nolock,tcp,noresvport
    - vers=3
    parameters:
      server: "23a9649583-iaq37.cn-shenzhen.nas.aliyuncs.com:/nasroot1/"
      driver: flexvolume
    provisioner: alicloud/nas
    reclaimPolicy: Delete
    ```

    **说明：**

    -   mountOptions：表示生成的PV options配置，挂载NAS卷时使用这个options挂载。
    -   server：表示生成目标PV所使用NAS挂载点列表。格式为 nfsurl1:/path1,nfsurl2:/path2；当配置多个server时，通过此StorageClass创建的PV会轮询使用上述server作为配置参数；极速NAS配置路径需要以/share开头。
    -   driver：支持Flexvolume、NFS两种驱动，默认为NFS。
    -   reclaimPolicy：PV的回收策略，建议配置为Retain。
        -   当配置为Delete时，删除PV后NAS文件系统中的对应目录会默认修改名字（例如，path-name会被修改为archived-path-name）。
        -   如果需要删除文件系统中对应的存储目录，可在StorageClass中配置`archiveOnDelete`为false。
2.  使用动态卷。

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


