---
keyword: [StorageClass, Default StorageClass]
---

# StorageClasses

This topic describes different types of StorageClasses for serverless Kubernetes \(ASK\) clusters. This topic also describes how to choose a proper StorageClass to meet your business requirements and how to configure a default StorageClass.

## StorageClass

By default, ASK clusters provide the following StorageClasses:

-   alicloud-disk-efficiency: ultra disk.
-   alicloud-disk-ssd: standard SSD.
-   alicloud-disk-essd: ESSD.
-   alicloud-disk-available: a high-availability mode. In this mode, the system attempts to create an SSD in priority. If the SSD resources are exhausted, the system attempts to create an ultra disk.

    **Note:** For versions earlier than alicloud-csi-provisioner v1.14.8.39-0d749258-aliyun, the system attempts to create a disk in the order of enhanced SSD, SSD, and ultra disk.

-   alicloud-disk-topology: creates a cloud disk in WaitForFirstConsumer mode.

The first four types of StorageClass are suitable for single-zone clusters. The last type of StorageClass is suitable for multi-zone clusters.

When you configure a StorageClass, the following rules determine the zone where the disk is created:

-   If you set volumeBindingMode: WaitForFirstConsumer in the StorageClass configuration, a disk is created in the zone where the pod is deployed.
-   If you set volumeBindingMode: Immediate and specify only one zone in the zoneId parameter of the StorageClass configuration, a disk is created in the specified zone.
-   If you set volumeBindingMode: Immediate and specify multiple zones in the zoneId parameter of the StorageClass configuration, the system attempts to create a disk in the specified zones in a round robin manner.
-   If you set volumeBindingMode: Immediate and do not specify the zoneId parameter in the StorageClass configuration, a disk is created in the zone where the csi-provisioner is deployed.

If your cluster is deployed across zones, we recommend that you set volumeBindingMode: WaitForFirstConsumer in the StorageClass configuration. You can create a StorageClass based on the type of disk that is required.

## Default StorageClass

Kubernetes provides the default StorageClass feature. If a persistent volume claim \(PVC\) does not require a specific StorageClass, you can use a default StorageClass to create a persistent volume \(PV\). For more information, see [Default StorageClass](https://kubernetes.io/docs/tasks/administer-cluster/change-default-storage-class/).

**Note:**

-   The default StorageClass applies to all PVCs. Exercise caution if your cluster has PVCs for multiple storage media. For example, you want to create a pair of PV and PVC of the NAS type. However, if you have already declared a default StorageClass, a PV of another type may be automatically created, such as a disk PV. Therefore, ASK clusters do not support the default StorageClass feature. If you want to configure a default StorageClass, perform the following steps.
-   You can configure only one default StorageClass for each cluster.

1.  Configure a default StorageClass.

    Run the following command to set alicloud-disk-ssd as the default StorageClass:

    ```
    kubectl patch storageclass alicloud-disk-ssd -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
    ```

    After the default StorageClass is configured, alicloud-disk-ssd is marked as \(default\).

    ```
    kubectl get sc
    ```

    The following output is returned:

    ```
    NAME                          PROVISIONER                       AGE
    alicloud-disk-ssd (default)   diskplugin.csi.alibabacloud.com   96m
    ```

2.  Use the default StorageClass.

    1.  Use the following template to create a PVC without specifying a StorageClass:

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

        The cluster automatically creates a PV based on the default StorageClass alicloud-disk-ssd.

        ```
        kubectl get pvc
        ```

        The following output is returned:

        ```
        NAME       STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS        AGE
        disk-pvc   Bound    d-bp18pbai447qverm3ttq   20Gi       RWO            alicloud-disk-ssd   49s
        ```


You can also run the following command to disable the default StorageClass:

```
kubectl patch storageclass alicloud-disk-ssd -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'
```

