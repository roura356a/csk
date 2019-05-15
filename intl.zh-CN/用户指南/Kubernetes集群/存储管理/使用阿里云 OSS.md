# 使用阿里云 OSS {#concept_h11_5bt_vdb .concept}

您可以在阿里云容器服务 Kubernetes 集群中使用阿里云 OSS 数据卷。

目前，仅支持 OSS 静态存储卷，不支持 OSS 动态存储卷。您可以通过以下方式使用 OSS 静态存储卷：

-   直接使用 volume 方式
-   使用 PV/PVC

## 前提条件 {#section_dyz_ybt_vdb .section}

使用 OSS 静态存储卷之前，您需要先在 OSS 管理控制台上创建 Bucket。

## 使用说明 {#section_dpz_zbt_vdb .section}

-   OSS 为共享存储，可以同时为多个 Pod 提供共享存储服务。
-   bucket：目前只支持挂载 Bucket，不支持挂载 Bucket 下面的子目录或文件。
-   url: OSS endpoint，挂载 OSS 的接入域名。
-   akId: 用户的 access id 值。
-   akSecret：用户的 access secret 值。
-   otherOpts: 挂载 OSS 时支持定制化参数输入，格式为: `-o *** -o ***`。

## 注意事项 {#section_gyz_ybt_vdb .section}

-   如果您的 Kubernetes 集群是在 2018 年 2 月 6 日之前创建的，那么您使用数据卷之间需要先安装[安装插件](intl.zh-CN/用户指南/Kubernetes集群/存储管理/安装插件.md#)。使用 OSS 数据卷必须在部署 flexvolume 服务的时候创建 Secret，并输入 AK 信息。

-   早期的flexvolume版本挂载的oss卷，在容器服务Kubernetes集群升级、kubelet重启过程中会有ossfs重启操作，OSS存储卷会重新挂载，使用OSS存储卷的Pod在升级后需要重建，可以通过升级flexvolume解决。

## 使用 OSS 静态卷 {#section_d13_bct_vdb .section}

**直接使用 volume 方式**

使用oss-deploy.yaml 文件创建 Pod。

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: nginx-oss-deploy
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx-flexvolume-oss
        image: nginx
        volumeMounts:
          - name: "oss1"
            mountPath: "/data"
        livenessProbe:
          exec:
            command:
            - sh
            - -c
            - cd /data
          initialDelaySeconds: 30
          periodSeconds: 30
      volumes:
        - name: "oss1"
          flexVolume:
            driver: "alicloud/oss"
            options:
              bucket: "docker"
              url: "oss-cn-hangzhou.aliyuncs.com"
              akId: ***
              akSecret: ***
              otherOpts: "-o max_stat_cache_size=0 -o allow_other"
```

**使用 PV/PVC**

**步骤 1 创建 PV**

您可以使用 yaml 文件或者通过容器服务控制台界面创建 PV。

**使用 yaml 文件创建 PV**

使用oss-pv.yaml文件创建 PV。

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-oss
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  storageClassName: oss
  flexVolume:
    driver: "alicloud/oss"
    options:
      bucket: "docker"
      url: "oss-cn-hangzhou.aliyuncs.com"
      akId: ***
      akSecret: ***
      otherOpts: "-o max_stat_cache_size=0 -o allow_other"
```

**通过控制台界面创建 OSS 数据卷**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**集群** \> **存储**，进入数据卷列表页面。
3.  选择所需的集群，单击页面右上角的**创建**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16689/155788442710740_zh-CN.png)

4.  在创建数据卷对话框中，配置数据卷的相关参数。

    -   **数据卷类型**：本示例中为 OSS。
    -   **数据卷名**：创建的数据卷的名称。数据卷名在集群内必须唯一。本例为 pv-oss。
    -   **数据卷总量**：所创建数据卷的容量。
    -   **访问模式**：默认为 ReadWriteMany。
    -   **AccessKey ID**、**AccessKey Secret**：访问 OSS 所需的 AccessKey。
    -   **Bucket ID**：您要使用的 OSS bucket 的名称。单击**选择Bucket**，在弹出的对话框中选择所需的 bucket 并单击**选择**。
    -   **访问域名**：如果 Bucket 和 ECS 实例位于不同地域（Region），请选择**外网域名**；如果位于相同地域，需要根据集群网络类型进行选择，若是 VPC 网络，请选择**VPC域名**，若是经典网络，请选择**内网域名**。
    -   **标签**：为该数据卷添加标签。
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16689/155788442710741_zh-CN.png)

5.  完成配置后，单击**创建**。

**步骤 2 创建 PVC**

使用oss-pvc.yaml文件创建 PVC。

```
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-oss
spec:
  storageClassName: oss
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 5Gi
```

**步骤 3 创建 Pod**

使用oss-pod.yaml创建 Pod。

```
apiVersion: v1
kind: Pod
metadata:
  name: "flexvolume-oss-example"
spec:
  containers:
    - name: "nginx"
      image: "nginx"
      volumeMounts:
          - name: pvc-oss
            mountPath: "/data"
      livenessProbe:
        exec:
          command:
          - sh
          - -c
          - cd /data
        initialDelaySeconds: 30
        periodSeconds: 30
  volumes:
  - name: pvc-oss
    persistentVolumeClaim:
        claimName: pvc-oss
```

## 使用 OSS 动态卷 {#section_ozz_ybt_vdb .section}

目前暂不支持。

