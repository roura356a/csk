---
keyword: [StorageClass, Default StorageClass]
---

# 存储类（StorageClass）

本文为您介绍集群默认提供的StorageClass存储类型和适用场景，以及如何配置Default StorageClass存储类型。

## StorageClass

ASK集群默认提供了以下几种StorageClass：

-   alicloud-disk-efficiency：高效云盘。
-   alicloud-disk-ssd：SSD云盘。
-   alicloud-disk-essd：ESSD云盘。
-   alicloud-disk-available：提供高可用选项，优先创建SSD云盘；如果SSD云盘售尽，则创建高效云盘。

    **说明：** 对于alicloud-csi-provisioner v1.14.8.39-0d749258-aliyun之前的版本，优先创建ESSD云盘；如果ESSD云盘售尽，则创建SSD云盘；如果SSD云盘售尽，则创建高效云盘。

-   alicloud-disk-topology: 使用延迟绑定的方式创建云盘。

以上5种StorageClass中，前4种可以在单可用区使用，最后一种更适合在多可用区使用。

通过StorageClass创建云盘：StorageClass配置volumeBindingMode: Immediate，且配置zoneId参数（一个可用区），则选择此可用区为创建云盘的可用区。

## Default StorageClass

Kubernetes提供Default StorageClass机制，您在PVC不指定StorageClass的情况下，可以通过Default StorageClass创建数据卷，请参见[Default StorageClass](https://kubernetes.io/docs/tasks/administer-cluster/change-default-storage-class/)。

**说明：**

-   由于Default StorageClass会对所有PVC起作用，对于具备不同类型存储卷能力的集群，需要小心使用。例如，您想生成一个NAS类型PVC、PV，并绑定PVC和PV，但可能因为有Default StorageClass而自动创建了云盘PV。基于上述原因，ASK集群没有提供Default StorageClass，如果您期望使用Default StorageClass，可以参考以下配置。
-   一个集群中最多配置一个Default StorageClass，否则默认能力将不起作用。

1.  配置Default StorageClass。

    执行以下命令将StorageClass （alicloud-disk-ssd）配置为一个Default StorageClass。

    ```
    kubectl patch storageclass alicloud-disk-ssd -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
    ```

    这时查询集群中的StorageClass可以看到alicloud-disk-ssd名字后面加了\(default\)字样。

    ```
    kubectl get sc
    ```

    返回结果如下：

    ```
    NAME                          PROVISIONER                       AGE
    alicloud-disk-ssd (default)   diskplugin.csi.alibabacloud.com   96m
    ```

2.  使用Default StorageClass。

    1.  使用以下模板创建一个没有配置StorageClass的PVC。

        ```
        apiVersion: v1
        kind: PersistentVolumeClaim
        metadata:
          name: disk-pvc
        spec:
          accessModes:
          - ReadWriteOnce
          resources:
            requests:
              storage: 20Gi
        ```

        集群会自动创建一个云盘卷（PV），且配置了Default StorageClass（alicloud-disk-ssd）。

        ```
        kubectl get pvc
        ```

        返回结果如下：

        ```
        NAME       STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS        AGE
        disk-pvc   Bound    d-bp18pbai447qverm3ttq   20Gi       RWO            alicloud-disk-ssd   49s
        ```


您可以通过以下命令取消默认存储类型配置。

```
kubectl patch storageclass alicloud-disk-ssd -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'
```

