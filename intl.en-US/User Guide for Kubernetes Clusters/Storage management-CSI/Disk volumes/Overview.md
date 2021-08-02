---
keyword: [disk volumes, usage notes]
---

# Overview

You can create volumes to mount Alibaba Cloud disks to a Container Service for Kubernetes \(ACK\) cluster. You can use the Contain Storage Interface \(CSI\) plug-in provided by Alibaba Cloud to mount disks by creating persistent volumes \(PVs\) and persistent volume claims \(PVCs\). A PV can be statically or dynamically provisioned. This topic describes the limits on disk volumes and other relevant requirements.

## Usage notes

-   A disk cannot be shared. If you use a disk by creating a pair of PV and PVC, the disk can be mounted to only one pod.
-   We recommend that you mount a disk to an application deployed by using a StatefulSet. If you mount a disk to an application deployed by using a Deployment, you must set the number of replicated pods to one. In addition, you cannot prioritize pods when you mount or unmount disks. We recommend that you do not mount a disk to an application deployed by using a Deployment.
-   Before you can use a statically provisioned disk volume, you must create the disk and obtain the disk ID. For more information, see [Create a disk](/intl.en-US/Block Storage/Cloud disks/Create a cloud disk/Create a disk.md).

    **Note:** Your disk must meet the following requirements:

    -   An ultra disk must have a minimum capacity of 20 GiB.
    -   A standard SSD must have a minimum capacity of 20 GiB.
    -   An enhanced SSD \(ESSD\) must have a minimum capacity of 20 GiB.
-   You can mount disks to only the nodes that are deployed in the same zone as the disks. Therefore, when you create a disk, select the zone of the pod to which you want to mount the disk.
-   The type of disk must match the Elastic Compute Service \(ECS\) instance types that are used in your cluster before you can mount a disk. For more information about the matching rules between disk types and ECS instance types, see [Instance families](/intl.en-US/Instance/Instance families.md).
-   Only pay-as-you-go disks can be mounted. If you change the billing method of an Elastic Compute Service \(ECS\) instance in the cluster from pay-as-you-go to subscription, you cannot change the billing method of its disks to subscription. Otherwise, the disks cannot be mounted to the cluster.
-   You can mount at most 16 disks to each node. The maximum capacity of each disk is 32 TiB.

## StorageClasses

**StorageClass**

ACK clusters support the following types of StorageClasses:

-   alicloud-disk-efficiency: ultra disk.
-   alicloud-disk-ssd: standard SSD.
-   alicloud-disk-essd: ESSD.
-   alicloud-disk-available: a high-availability mode. In this mode, the system first attempts to create a standard SSD. If SSD resources are exhausted, the system attempts to create an ultra disk.

    **Note:**

    -   For alicloud-csi-provisioner versions earlier than alicloud-csi-provisioner v1.14.8.39-0d749258-aliyun, the system first attempts to create an enhanced SSD.
    -   If enhanced SSDs are out of stock, the system attempts to create a standard SSD.
    -   If standard SSDs are out of stock, the system attempts to create an ultra disk.
-   alicloud-disk-topology: creates a disk in WaitForFirstConsumer mode.

The first four types of StorageClasses are suitable for clusters that are created in one zone. The last type of StorageClass is suitable for clusters that are created across zones.

When you configure a StorageClass, the following rules determine the zone where the disk is created:

-   Specify volumeBindingMode: WaitForFirstConsumer in the StorageClass configurations to create a disk in the zone where the pod that uses the disk volume is deployed.
-   Specify volumeBindingMode: Immediate and specify a zone by setting the zoneId parameter in the StorageClass configurations to create a disk in the specified zone.
-   Specify volumeBindingMode: Immediate and specify multiple zones by setting the zoneId parameter in the StorageClass configurations to create a disk in one of the specified zones.
-   Specify volumeBindingMode: Immediate without setting the zoneId parameter in the StorageClass configurations to create a disk in the zone where csi-provisioner is deployed.

If your cluster is deployed across zones, we recommend that you set volumeBindingMode: WaitForFirstConsumer in the StorageClass configurations. You can create a StorageClass based on the type of disk that is required.

**Default StorageClass**

Kubernetes provides the default StorageClass feature. If a PVC does not specify a StorageClass, the default StorageClass is used to provision a PV for the PVC. For more information, see [Default StorageClass](https://kubernetes.io/docs/tasks/administer-cluster/change-default-storage-class/).

**Note:**

-   The default StorageClass applies to all PVCs. Proceed with caution if your cluster has PVCs for multiple storage media. For example, the default StorageClass may create a disk PV for a PVC that defines an Apsara File Storage NAS \(NAS\) file system. Therefore, ACK clusters do not provide default StorageClasses. If you want to configure a default StorageClass, perform the following steps.
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

    Expected output:

    ```
    NAME                          PROVISIONER                       AGE
    alicloud-disk-ssd (default)   diskplugin.csi.alibabacloud.com   96m
    ```

2.  Use the default StroageClass.

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

        The cluster automatically creates a disk PV based on the default StorageClass alicloud-disk-ssd.

        ```
        kubectl get pvc
        ```

        Expected output:

        ```
        NAME       STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS        AGE
        disk-pvc   Bound    d-bp18pbai447qverm3ttq   20Gi       RWO            alicloud-disk-ssd   49s
        ```


You can also run the following command to disable the default StorageClass:

```
kubectl patch storageclass alicloud-disk-ssd -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'
```

