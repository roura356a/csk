# Flexvolume驱动-静态NAS卷 {#task_1715570 .task}

可以通过阿里云提供的 Flexvolume 插件使用阿里云 NAS 文件存储服务。

使用 Flexvolume 插件，您可以通过 Volume 方式使用阿里云 NAS 数据卷或者通过 PV/PVC 方式使用阿里云 NAS 数据卷。

参数说明：

-   server：为 NAS 数据盘的挂载点。
-   path：为连接 NAS 数据卷的挂载目录，支持挂载 NAS 子目录，且当子目录不存在时，会自动创建子目录并挂载。极速NAS的Path需要以 /share 开头。
-   vers：定义 NFS 挂载协议的版本号，支持v3 和 v4.0，默认使用版本v3且推荐使用此版本。
-   mode：定义挂载目录的访问权限，注意挂载 NAS 盘根目录时不能配置挂载权限。当 NAS 盘中数据量很大时，配置 mode 会导致执行挂载非常慢，甚至挂载失败。
-   options：定义挂载参数，不配置此项时，v3版本的挂载默参数为`nolock,tcp,noresvport`，v4.0版本挂载的默认参数为`noresvport`。

## 直接通过 volume 使用 {#section_7na_s7z_pps .section}

使用nas-deploy.yaml文件创建 Pod。

1.  创建并复制以下内容到nas-deploy.yaml中。 

    ``` {#codeblock_pa7_fum_f65}
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nas-static
      labels:
        app: nginx
    spec:
      replicas: 1
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
            image: nginx
            ports:
            - containerPort: 80
            volumeMounts:
              - name: nas1
                mountPath: "/data"
          volumes:
          - name: "nas1"
            flexVolume:
              driver: "alicloud/nas"
              options:
                server: "0cd8b4a576-grs79.cn-hangzhou.nas.aliyuncs.com"
                path: "/k8s"
                vers: "3"
                options: "nolock,tcp,noresvport"
    ```

2.  执行如下命令，创建Pod。 

    ``` {#codeblock_dxm_0j3_1b9}
    kubectl apply -f nas-deploy.yaml
    ```


**说明：** 

`volumes`中，`name`的取值建议和`volumeId`的取值保持一致。

如果您所使用的集群是多可用区的模式，使用云盘时需要进行可用区调度，即把 Pod 调度到与云盘相同的可用区。

具体配置为在 yaml 中对 pod 添加 nodeSelector，示例如下：

``` {#codeblock_6qr_5dm_tog}
nodeSelector:
        failure-domain.beta.kubernetes.io/zone: cn-hangzhou-b
```

## 通过 PV/PVC 使用 {#section_1zn_snf_pi7 .section}

1.  创建 PV 您可以使用 yaml 文件或者通过阿里云容器服务控制台界面创建 NAS 数据卷。
    -   通过 yaml 文件创建 PV

        使用 nas-pv.yaml 文件创建 PV。

        ``` {#codeblock_586_syq_5dv}
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
              options: "nolock,tcp,noresvport"
        ```

    -   通过控制台界面创建云盘数据卷
        1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
        2.  在 Kubernetes 菜单下，单击左侧导航栏中的**集群** \> **存储卷**，进入存储与存储声明页面。
        3.  在**存储卷**页签，选择所需的集群，单击**创建**。

            ![创建云盘存储卷](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16726/156647043810672_zh-CN.png)

        4.  在创建存储卷对话框中，配置存储卷的相关参数。

            ![创建存储卷](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1371788/156647043855864_zh-CN.png)

            -   **存储卷类型**：本示例中为NAS。
            -   **数据卷名**：创建的数据卷的名称。数据卷名在集群内必须唯一。本例为 pv-nas。
            -   **总量**：所创建存储卷的容量。注意不能超过磁盘容量。
            -   **访问模式**：默认为 ReadWriteMany。
            -   **挂载点域名**：集群在 NAS 文件系统中挂载点的挂载地址。
            -   **子目录**：NAS 路径下的子目录，以/开头，设定后数据卷将挂载到指定的子目录。
                -   如果 NAS 根目录下没有此子目录，会默认创建后再挂载。
                -   您可以不填此项，默认挂载到 NAS 根目录。
                -   极速NAS需要以/share开头。
            -   **权限**：设置挂载目录的访问权限，例如：755、644、777 等。
                -   只有挂载到 NAS 子目录时才能设置权限，挂载到根目录时不能设置。
                -   您可以不填此项，默认权限为 NAS 文件原来的权限。
                -   当挂载的目录文件量较大时不建议此配置，否则会出现chmod长时间执行。
            -   **版本**：所创建存储卷的版本。
            -   **标签**：为该存储卷添加标签。
        5.  完成配置后，单击**创建**。
2.  创建 PVC 使用 nas-pvc.yaml 文件创建 PVC。

    ``` {#codeblock_6lr_4m1_ax5}
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

3.  创建 Pod 使用nas-pod.yaml文件创建 pod。

    ``` {#codeblock_634_9kj_fzr}
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nas-static
      labels:
        app: nginx
    spec:
      replicas: 1
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
            image: nginx
            ports:
            - containerPort: 80
            volumeMounts:
              - name: pvc-nas
                mountPath: &quot;/data&quot;
          volumes:
            - name: pvc-nas
              persistentVolumeClaim:
                claimName: pvc-nas
    ```


