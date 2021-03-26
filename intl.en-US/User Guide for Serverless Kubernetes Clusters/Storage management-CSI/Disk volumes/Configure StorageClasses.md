---
keyword: [StorageClass, Default StorageClass]
---

# Configure StorageClasses

This topic describes different types of StorageClass for serverless Kubernetes \(ASK\) clusters. This topic also describes how to choose proper StorageClass types to meet your business requirements and how to configure a default StorageClass.

## StorageClass

The following types of StorageClass are supported in an ASK cluster.

-   alicloud-disk-efficiency: ultra disk.
-   alicloud-disk-ssd: standard SSD.
-   alicloud-disk-essd: ESSD.
-   alicloud-disk-available: a high-availability mode. In this mode, the system attempts to create an SSD in priority. If the SSD resources are exhausted, the system attempts to create an ultra disk.

    **Note:** For versions earlier than alicloud-csi-provisioner V1.14.8.39-0d749258-aliyun, the system attempts to create a disk in the priority order of enhanced SSD, SSD, and ultra disk.

-   alicloud-disk-topology: creates a cloud disk in WaitForFirstConsumer mode.

The first four types of StorageClass are suitable for single-zone clusters. The last type of StorageClass is suitable for multi-zone clusters.

If you specify volumeBindingMode: Immediate and specify only one zone ID in the zoneId parameter of the StorageClass configurations, a cloud disk is created in the specified zone.

## Default StorageClass

Kubernetes provides the default StorageClass feature. If a persistent volume claim \(PVC\) does not specify a StorageClass, the default StorageClass is used to provision a persistent volume \(PV\) for the PVC. For more information, see [Default StorageClass](https://kubernetes.io/docs/tasks/administer-cluster/change-default-storage-class/).

**Note:**

-   The default StorageClass applies to all PVCs. Exercise caution if your cluster has PVCs for multiple storage media. For example, the default StorageClass may create a cloud disk as the PV for a PVC that claims an Apsara File Storage NAS \(NAS\) file system. To avoid such situations, ASK clusters do not support the default StorageClass feature. To configure a default StorageClass, perform the following steps.
-   You can configure only one default StorageClass for each cluster. If you configure more than one default StorageClass for a cluster, all default StorageClasses become invalid.

1.  Configure a default StorageClass.

    Run the following command to set alicloud-disk-ssd as a default StorageClass:

    ```
    kubectl patch storageclass alicloud-disk-ssd -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
    ```

    After the default StorageClass is configured, alicloud-disk-ssd is marked as \(default\)

    ```
    kubectl get sc
    ```

    The following output is returned:

    ```
    NAME                          PROVISIONER                       AGE
    alicloud-disk-ssd (default)   diskplugin.csi.alibabacloud.com   96m
    ```

2.  Use the default StroageClass.

    1.  The following code provides an example on how to create a PVC without specifying a StorageClass:

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

        The cluster automatically creates a cloud disk as the PV based on the default StorageClass alicloud-disk-ssd.

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

