---
keyword: [LVM数据卷, 本地存储]
---

# LVM数据卷

您可以在本地盘上通过VolumeGroup进行磁盘虚拟化，并通过LVM数据卷切分磁盘给应用使用。本文介绍如何使用LVM数据卷。

使用HostPath和LocalVolume都可以实现Pod对主机存储空间的访问，但均具有其局限性：

-   Kubernetes没有提供上述本地存储卷的生命周期管理能力，需要管理员手动管理、运维存储卷。
-   多个Pod共享一个本地存储的时候，需要共享存储目录或者分别使用其子目录，无法做到容量隔离。
-   多个Pod共享一个本地存储的时候，IOPS、吞吐等指标共享了整个存储空间，无法进行限制。
-   新建Pod使用本地存储时，不知道各个节点存储空间余量，不能进行合理的存储卷调度。

为此ACK提供了LVM数据卷方案，以解决上述问题。

## 功能介绍

-   LVM数据卷生命周期管理：卷自动创建、删除、挂载、卸载。
-   LVM数据卷扩容功能。
-   LVM卷的监控能力。
-   LVM卷的IOPS限制。
-   节点本地存储管理：自动运维VolumeGroup。
-   LVM卷的集群容量感知能力。

## 注意事项

-   LVM为本地存储类型，不适用于高可用数据场景。
-   VolumeGroup运维、本地存储容量感知为可选项（暂缓提供）。

## 实现架构

![实现架构](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/2679954161/p244778.png)

基础的LVM功能（卷的生命周期管理、扩容、挂载、格式化等）由CSI-Provisioner和CSI-Plugin实现。

|单元|详情|
|--|--|
|存储管理器|节点本地存储（VolumeGroup）的运维管理以及容量统计。可以不用该组件，由Worker管理运维VolumeGroup。|
|存储信息中心|保存节点本地存储信息，包括容量、VolumeGroup等信息。|
|本地存储调度器|实现新建PVC对集群存储容量感知功能。|

## 插件部署

LVM CSI插件分为2个组件：Plugin（负责挂载、卸载LVM卷）和Provisioner（负责创建LVM卷和PV对象）。



## 使用LVM数据卷示例

使用CSI-Provisioner自动创建PV，有以下特点：

-   StorageClass中需要指定VolumeGroup名字。
-   如果期望创建的PV在某个节点，需要给PVC添加Annotations：volume.kubernetes.io/selected-node: nodeName。

1.  使用以下模板创建StorageClass。

    ```
    apiVersion: storage.k8s.io/v1
    kind: StorageClass
    metadata:
        name: csi-local
    provisioner: localplugin.csi.alibabacloud.com
    parameters:
        volumeType: LVM
        vgName: volumegroup1
        fsType: ext4
        lvmType: "striping"
    reclaimPolicy: Delete
    volumeBindingMode: WaitForFirstConsumer
    allowVolumeExpansion: true
    ```

    |参数|描述|
    |--|--|
    |volumeType|表示本地存储类型为LVM（后续将支持其他类型本地存储）。|
    |vgName|必选，VolumeGroup的名字。|
    |lvmType|生成的LVM类型，支持linear（线性）、striping（条带化）。|
    |fsType|格式文件系统类型。|

2.  使用以下模板创建PVC。

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: lvm-pvc
    spec:
      accessModes:
      - ReadWriteOnce
      resources:
        requests:
          storage: 2Gi
      storageClassName: csi-local
    ```

3.  使用以下模板创建应用。

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: deployment-lvm
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
            image: nginx:1.7.9
            volumeMounts:
              - name: lvm-pvc
                mountPath: "/data"
          volumes:
            - name: lvm-pvc
              persistentVolumeClaim:
                claimName: lvm-pvc
    ```

4.  查看应用状态。

    执行以下命令查看Pod信息。

    ```
    kubectl get pod
    ```

    预期输出：

    ```
    NAME                             READY   STATUS    RESTARTS   AGE
    deployment-lvm-9f798687c-m****   1/1     Running   0          9s
    ```

    执行以下命令查看PVC信息。

    ```
    kubectl get pvc
    ```

    预期输出：

    ```
    NAME      STATUS   VOLUME                                      CAPACITY   ACCESS MODES   STORAGECLASS   AGE
    lvm-pvc   Bound    disk-afacf7a9-3d1a-45da-b443-24f8fb35****   2Gi        RWO            csi-local      16s
    ```

    执行以下命令查看PV信息。

    ```
    kubectl get pv
    ```

    预期输出：

    ```
    NAME                                        CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM             STORAGECLASS   REASON   AGE
    disk-afacf7a9-3d1a-45da-b443-24f8fb35****   2Gi        RWO            Delete           Bound    default/lvm-pvc   csi-local               12s
    ```

    执行以下命令查看Pod挂载详情。

    ```
    kubectl exec -ti deployment-lvm-9f798687c-m**** sh df /data
    ```

    预期输出：

    ```
    Filesystem                                                                1K-blocks  Used   Available Use% Mounted on
    /dev/mapper/volumegroup1-disk--afacf7a9--3d1a--45da--b443--24f8fb35****   1998672    6144   1976144   1%   /data
    ```

    执行以下命令列出/data下的目录。

    ```
    ls /data
    ```

    预期输出：

    ```
    lost+found
    ```

    执行以下命令在/data下新增test目录并查看。

    ```
    touch /data/test
    ls /data
    ```

    预期输出：

    ```
    lost+found  test
    ```

    执行以下命令退出登录。

    ```
    exit
    ```

    执行以下命令删除Pod重建。

    ```
    kubectl delete pod deployment-lvm-9f798687c-m****
    ```

    预期输出：

    ```
    pod "deployment-lvm-9f798687c-m****" deleted
    ```

    执行以下命令查看Pod信息。

    ```
    kubectl get pod
    ```

    预期输出：

    ```
    NAME                             READY   STATUS    RESTARTS   AGE
    deployment-lvm-9f798687c-j****   1/1     Running   0          2m19s
    ```

    执行以下命令查看Pod挂载详情。

    ```
    kubectl exec deployment-lvm-9f798687c-j**** ls /data
    ```

    预期输出：

    ```
    lost+found  
    test
    ```

5.  扩容LVM卷。

    执行以下命令查看PVC信息。

    ```
    kubectl get pvc
    ```

    预期输出：

    ```
    NAME      STATUS   VOLUME                                      CAPACITY   ACCESS MODES   STORAGECLASS   AGE
    lvm-pvc   Bound    disk-afacf7a9-3d1a-45da-b443-24f8fb35****   2Gi        RWO            csi-local      6m50s
    ```

    执行以下命令将PVC扩容到4 GiB。

    ```
    kubectl patch pvc lvm-pvc -p '{"spec":{"resources":{"requests":{"storage":"4Gi"}}}}'
    ```

    预期输出：

    ```
    persistentvolumeclaim/lvm-pvc patched
    ```

    执行以下命令查看PVC信息。

    ```
    kubectl get pvc
    ```

    预期输出：

    ```
    NAME      STATUS   VOLUME                                      CAPACITY   ACCESS MODES   STORAGECLASS   AGE
    lvm-pvc   Bound    disk-afacf7a9-3d1a-45da-b443-24f8fb35****   4Gi        RWO            csi-local      7m26s
    ```

    执行以下命令查看LVM卷扩容到4 GiB。

    ```
    kubectl exec deployment-lvm-9f798687c-j**** df /data
    ```

    预期输出：

    ```
    Filesystem                                                                1K-blocks  Used   Available Use% Mounted on
    /dev/mapper/volumegroup1-disk--afacf7a9--3d1a--45da--b443--24f8fb35****   4062912    8184   4038344   1%   /data
    ```

6.  执行以下命令监控LVM卷。

    ```
    curl -s localhost:10255/metrics | grep lvm-pvc
    ```

    预期输出：

    ```
    kubelet_volume_stats_available_bytes{namespace="default",persistentvolumeclaim="lvm-pvc"} 1.917165568e+09
    kubelet_volume_stats_capacity_bytes{namespace="default",persistentvolumeclaim="lvm-pvc"} 1.939816448e+09
    kubelet_volume_stats_inodes{namespace="default",persistentvolumeclaim="lvm-pvc"} 122400
    kubelet_volume_stats_inodes_free{namespace="default",persistentvolumeclaim="lvm-pvc"} 122389
    kubelet_volume_stats_inodes_used{namespace="default",persistentvolumeclaim="lvm-pvc"} 11
    kubelet_volume_stats_used_bytes{namespace="default",persistentvolumeclaim="lvm-pvc"} 5.873664e+06
    ```

    可以将上述监控数据接入Prometheus并在前端展示。具体操作，请参见[开源Prometheus监控](/cn.zh-CN/Kubernetes集群用户指南/可观测性/监控管理/开源Prometheus监控.md)。


