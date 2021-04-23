---
keyword: [QuotaPath, 本地存储卷]
---

# QuotaPath数据卷

您可以在本地盘上通过文件系统（如Ext4）的ProjectQuota功能，实现目录级别的容量Quota控制，通过CSI插件实现QuotaPath数据卷的切分、限额、挂载等生命周期管理。本文介绍如何使用QuotaPath数据卷。

已部署LVM CSI插件。具体步骤，请参见[插件部署](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/本地存储卷/LVM数据卷.md)。

## QuotaPath与HostPath、LVM的区别

使用HostPath、LVM、QuotaPath都可以实现Pod对主机存储空间的访问，但其各具特点：

-   HostPath将目录进行切分，多个目录间共享相同存储设备的空间、IO等资源。
-   LVM将设备进行虚拟化，然后切分成多个卷，每个卷都独立拥有存储限额。
-   QuotaPath使用文件系统的Quota功能，将目录进行切分，每个目录拥有独立的存储限额。

## 功能介绍

-   QuotaPath数据卷生命周期管理：自动创建、删除、挂载、卸载。
-   QuotaPath数据卷扩容功能。
-   节点本地存储管理：自动运维QuotaPath根目录。
-   QuotaPath卷的集群容量感知能力。

## 注意事项

-   QuotaPath为本地存储类型，不适用于高可用数据场景。
-   QuotaPath根目录运维、本地存储容量感知为可选项（暂缓提供）。

## 使用QuotaPath示例

使用CSI-Provisioner自动创建PV，有以下特点：

-   StorageClass中需要指定rootPath名字。
-   如果期望创建的PV在某个节点，需要给PVC添加Label：volume.kubernetes.io/selected-node: nodeName。

1.  使用以下模板创建StorageClass。

    ```
    apiVersion: storage.k8s.io/v1
    kind: StorageClass
    metadata:
      name: alicloud-local-quota
    parameters:
      volumeType: QuotaPath
      rootPath: /mnt/quota
    provisioner: localplugin.csi.alibabacloud.com
    reclaimPolicy: Delete
    allowVolumeExpansion: true
    volumeBindingMode: WaitForFirstConsumer
    ```

    |参数|描述|
    |--|--|
    |volumeType|本地存储类型，本示例中存储类型为QuotaPath。|
    |rootPath|可选，表示QuotaPath所在目录名称。|

2.  使用以下模板创建PVC。

    默认CSI插件会使用/mnt/quotapath.namespacex.x作为QuotaPath的根目录，所有的PV都将在这个目录下面分配并创建子目录。

    可以在PVC上添加以下Annotation，用来自定义QuotaPath父目录volume.kubernetes.io/selected-storage: /mnt/xxx。

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: csi-quota
    spec:
      accessModes:
      - ReadWriteOnce
      resources:
        requests:
          storage: 2Gi
      storageClassName: alicloud-local-quota
    ```

3.  使用以下模板创建应用。

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
      selector:
        app: nginx
    ---
    apiVersion: apps/v1
    kind: StatefulSet
    metadata:
      name: web-quota
    spec:
      selector:
        matchLabels:
          app: nginx
      serviceName: "nginx"
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx
            volumeMounts:
            - name: disk-ssd
              mountPath: /data
          volumes:
            - name: "disk-ssd"
              persistentVolumeClaim:
                claimName: csi-quota
    ```

4.  查看应用状态。

    执行以下命令查看Pod信息。

    ```
    kubectl get pod |grep quota
    ```

    返回结果如下：

    ```
    NAME          READY   STATUS    RESTARTS   AGE
    web-quota-0   1/1     Running   0          16s
    ```

    执行以下命令查看PVC信息。

    ```
    kubectl get pvc
    ```

    返回结果如下：

    ```
    NAME        STATUS   VOLUME                CAPACITY   ACCESS MODES   STORAGECLASS           AGE
    csi-quota   Bound    local-f4b129a5-****   2Gi        RWO            alicloud-local-quota   48s
    ```

    执行以下命令查看PV信息。

    ```
    kubectl get pv |grep quota
    ```

    返回结果如下：

    ```
    NAME                  CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM               STORAGECLASS           REASON   AGE
    local-f4b129a5-****   2Gi        RWO            Delete           Bound    default/csi-quota   alicloud-local-quota            66s
    ```

    执行以下命令查看Pod挂载详情。

    ```
    kubectl exec -ti web-quota-0 sh
    df |grep data
    ```

    返回结果如下：

    ```
    Filesystem  1K-blocks  Used Available Use% Mounted on
    /dev/vdd    2097152       4   2097148   1% /data
    ```

    执行以下命令列出/data下的目录。

    ```
    ls /data
    ```

    返回结果如下：

    ```
    lost+found
    ```

    执行以下命令在/data下新增test目录并查看。

    ```
    touch /data/test
    ls /data
    ```

    返回结果如下：

    ```
    lost+found test
    ```

5.  扩容数据卷。

    执行以下命令查看PVC信息。

    ```
    kubectl get pvc
    ```

    返回结果如下：

    ```
    NAME        STATUS   VOLUME                CAPACITY   ACCESS MODES   STORAGECLASS           AGE
    csi-quota   Bound    local-f4b129a5-****   2Gi        RWO            alicloud-local-quota   42s
    ```

    执行以下命令将PVC扩容到3 GiB。

    ```
    kubectl patch pvc csi-quota -p '{"spec":{"resources":{"requests":{"storage":"3Gi"}}}}'
    ```

    返回结果如下：

    ```
    persistentvolumeclaim/csi-quota patched
    ```

    执行以下命令查看PVC信息。

    ```
    kubectl get pvc
    ```

    返回结果如下：

    ```
    NAME        STATUS   VOLUME                CAPACITY   ACCESS MODES   STORAGECLASS           AGE
    csi-quota   Bound    local-f4b129a5-****   3Gi        RWO            alicloud-local-quota   4m30s
    ```

    执行以下命令查看数据卷扩容到3 GiB。

    ```
    kubectl exec -ti web-quota-0 sh
    df |grep data
    ```

    返回结果如下：

    ```
    Filesystem  1K-blocks  Used Available Use% Mounted on
    /dev/vdd    3145728       4   3145724   1% /data
    ```


