---
keyword: [静态存储卷, NAS, Flexvolume]
---

# 使用NAS静态存储卷

您可以通过阿里云提供的Flexvolume插件使用阿里云NAS文件存储服务。本文介绍如何使用NAS静态存储卷。

-   使用存储卷时请将Flexvolume插件更新到最新版本。具体操作，请参见[安装与升级Flexvolume组件](/intl.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/安装与升级Flexvolume组件.md)。
-   已通过kubectl连接Kubernetes集群。具体操作，请参见[通过kubectl工具连接集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl工具连接集群.md)。

使用Flexvolume插件，您可以通过PV和PVC方式使用阿里云NAS存储卷。

## 注意事项

若您在应用模板中配置了securityContext.fsgroup参数，kubelet在存储卷挂载完成后会执行`chmod`或`chown`操作，导致挂载时间过长。

## 通过PV和PVC使用

Pod可以通过关联创建的PV和PVC的方式使用NAS存储卷。

1.  创建PV。

    您可以使用YAML文件或者通过阿里云容器服务控制台界面创建NAS存储卷。

    -   通过YAML文件创建PV。

        使用nas-pv.yaml文件创建PV。

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
              server: "0cd8b4a576-u****.cn-hangzhou.nas.aliyuncs.com"
              path: "/k8s"
              vers: "3"
              options: "nolock,tcp,noresvport"
        ```

    -   通过控制台界面创建NAS存储卷。
        1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
        2.  在控制台左侧导航栏中，单击**集群**。
        3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。
        4.  在集群管理页左侧导航栏中，选择**存储** \> **存储卷**。
        5.  在**存储卷**页面的右上角单击**创建**。
        6.  在创建存储卷对话框中，配置存储卷的相关参数。

            |参数|描述|
            |--|--|
            |**存储卷类型**|本示例中为NAS。|
            |**名称**|创建的存储卷名称。存储卷名在集群内必须唯一。本例为**pv-nas**。|
            |**存储驱动**|本示例选择Flexvolume。有关存储插件详细信息，请参见[CSI和Flexvolume存储插件的区别](/intl.zh-CN/Kubernetes集群用户指南/存储-CSI/存储CSI概述.md)。|
            |**总量**|所创建存储卷的容量。注意不能超过NAS文件系统的存储容量。|
            |**访问模式**|默认为ReadWriteMany。|
            |**挂载点域名**|集群在NAS文件系统中挂载点的挂载地址。关于NAS文件系统挂载点的管理，请参见[管理挂载点]()。|
            |**子目录**|NAS路径下的子目录，以/开头，设定后存储卷将挂载到指定的子目录。            -   如果NAS根目录下没有此子目录，会默认创建后再挂载。
            -   您可以不填此项，默认挂载到NAS根目录。
            -   极速NAS需要以/share开头。 |
            |**权限**|设置挂载目录的访问权限，例如755、644、777等。**说明：**

            -   只有挂载到NAS子目录时才能设置权限，挂载到根目录时不能设置。
            -   当挂载的目录文件量较大时不建议此配置，否则会出现chmod长时间执行。
如果挂载到NAS子目录时，您可以选择设置权限，或者不填此项。

            -   不填此项，默认权限为NAS文件原来的权限。
            -   选择设置权限时：
                -   如果是Flexvolume v1.14.6.15-8d3b7e7-aliyun以前版本，则使用递归方式进行权限操作，挂载目录下面所有文件、目录都会被修改权限。
                -   如果是Flexvolume v1.14.6.15-8d3b7e7-aliyun以及以后版本，配置了此项，按照**权限模式**的配置执行权限操作。 |
            |**权限模式**|定义权限变更方式，支持非递归或递归方式。            -   非递归：执行权限变更时，只对挂载目录起作用，其子目录、包含的文件不进行变更权限。
            -   递归：执行权限变更时，会对其子目录、包含的文件进行递归操作，全部变更权限。

**说明：** 当挂载目录下面文件数量较多时，使用递归方式，会出现执行chmod耗时长，从而导致挂载、卸载操作失败的可能，请谨慎使用。 |
            |**版本**|挂载的NAS卷使用的NFS协议版本号，推荐使用V3，且极速类型NAS只支持V3。|
            |**标签**|为该存储卷添加标签。|

        7.  完成配置后，单击**创建**。
2.  创建PVC。

    使用nas-pvc.yaml文件创建PVC。

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

3.  创建Pod。

    使用nas-pod.yaml文件创建Pod。

    ```
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
                mountPath: /data
          volumes:
            - name: pvc-nas
              persistentVolumeClaim:
                claimName: pvc-nas
    ```


[NAS存储卷概述](/intl.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/NAS存储卷/NAS存储卷概述.md)

