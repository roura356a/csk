---
keyword: [NAS卷的子目录, 配额限制]
---

# 设置NAS卷子目录的配额限制

配额限制可以确保您在资源分配的基础上通过资源限制提升整体资源的利用率。阿里云Kubernetes CSI支持对NAS卷的子目录进行配额限制。本文介绍如何对NAS卷子目录进行配额限制。

-   csi-plugin的镜像版本不低于v1.18.8.45。关于csi-plugin的版本说明，请参见[csi-plugin](/intl.zh-CN/产品发布记录/组件介绍与变更记录/存储/csi-plugin.md)。
-   NAS卷需要使用子目录进行挂载。

## 使用限制

-   目前仅容量型NAS支持使用配额限制，其他NAS类型均不支持。关于NAS卷的规格类型，请参见[NAS规格类型]()。
-   仅支持子目录挂载方式设置配额。
-   除呼和浩特和乌兰察布外其他地域均已支持配额限制功能。
-   对于单个文件系统，最多只能对10个目录设置配额。
    -   设置限制型配额后，如果文件使用量超过限制会导致增加文件长度，创建文件、目录和特殊文件，移动文件到目录等写入操作失败，应用层会收到IOError。
    -   由于限制型配额的高风险性，强烈建议您在业务关键路径上谨慎评估和测试验证后再配置限制型配额。
    -   NAS配额的设置为异步执行，因此限制型配额的生效和失效都有延迟，正常情况下需要等待5~15分钟。

## 使用示例

1.  创建带有配额子目录NAS的StorageClass。

    示例模板如下所示：

    ```
    apiVersion: storage.k8s.io/v1
    kind: StorageClass
    metadata:
      name: alicloud-nas-sp8
    mountOptions:
      - nolock,tcp,noresvport
      - vers=3
    parameters:
      volumeAs: subpath
      server: "xxx.cn-hangzhou.nas.aliyuncs.com:/"
      archiveOnDelete: "false"
      path: "/abc"
      volumeCapacity: "true"
    provisioner: nasplugin.csi.alibabacloud.com
    reclaimPolicy: Delete
    ```

    |参数|描述|
    |--|--|
    |mountOptions|挂载NAS的options参数在mountOptions中配置，包括NFS协议版本。|
    |volumeAs|可选subpath、filesystem，分别表示创建子目录类型的PV和文件系统类型PV。|
    |server|表示创建子目录类型的PV时，NAS文件系统的挂载点地址。|
    |archiveOnDelete|表示在reclaimPolicy为Delete时，是否删除后端存储。因为NAS为共享存储，添加此选项进行双重确认。默认为true。|
    |path|表示挂载子目录，极速NAS需要以/share开头。|
    |volumeCapacity|表示是否使用配额。可选值为：true，false。|
    |provisioner|表示ACK动态卷控制器名称。|
    |reclaimPolicy|表示PV的回收策略。可选值为：    -   Retain：保留后端存储，删除PV及PVC不会删除对应的后端存储，例如云盘。
    -   Delete：当删除PVC时，自动删除PV和后端的存储。 |

    **说明：** 创建带有配额子目录NAS的StorageClass，需要将volumeCapacity设置为true。

2.  创建容量大小为20 GiB的PVC。

    示例模板如下所示：

    ```
    kind: PersistentVolumeClaim
    apiVersion: v1
    metadata:
      name: pvc-nas-dynamic-create-subpath8
    spec:
      accessModes:
        - ReadWriteMany
      storageClassName: alicloud-nas-sp8
      resources:
        requests:
          storage: 20Gi
    ```

3.  创建Deployment，引用[步骤2](#step_h63_iin_svv)创建的PVC。

    示例模板如下所示：

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: deployment-nas-dynamic-create8
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
              image: nginx:1.14.2
              ports:
                - containerPort: 80
              volumeMounts:
                - name: pvc-nas-dynamic-create-subpath8
                  mountPath: "/data"
          volumes:
            - name: pvc-nas-dynamic-create-subpath8
              persistentVolumeClaim:
                claimName: pvc-nas-dynamic-create-subpath8
    ```


**结果验证**

1.  执行以下命令向[步骤3](#step_f1o_9un_06x)Deployment挂载的/data目录写入10 G数据。

    ```
    dd if=/dev/zero of=10G.txt bs=1M count=10000
    ```

2.  等待5~15分钟后，查看子目录的配额详情。

    1.  登录[NAS控制台](https://nas.console.aliyun.com/)。

    2.  在控制台左侧导航栏中单击**文件系统** \> **文件系统列表**。

    3.  选择目标文件系统**操作**列下的**更多** \> **配额管理**。

    4.  在目标文件系统的配额管理页面单击**操作**列的**管理配额**。

        可以看到子目录的容量限制为20 GiB，当前容量为9 GiB。

        ![配额管理](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2891466161/p254150.png)

        当此子目录写满20 GiB，再写入数据时，会提示超过磁盘配额，即`Disk quota exceeded`。

        ![磁盘配额](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1305466161/p254667.png)


