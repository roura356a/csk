# Provision Alibaba Cloud disks as dynamic volumes

To provision an Alibaba Cloud disk as a dynamic persistent volume \(PV\), you must manually create a StorageClass, and set the storageClassName field in a persistent volume claim \(PVC\) to specify the disk type.

Before you provision a dynamic volume, make sure that you have completed the following tasks:

-   [Create a cluster of ACK Proprietary Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Proprietary Edition.md)
-   [Use kubectl to connect to a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Manage and access clusters/Use kubectl to connect to a cluster.md)

For more information about StorageClass, see [Configure StorageClasses](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Disk volumes/Configure StorageClasses.md).

## Create a StorageClass

You can create a StorageClass in the following ways:

-   Create a StorageClass in WaitForFirstConsumer mode.

    For a multi-zone cluster, the WaitForFirstConsumer binding mode resolves the issue where Elastic Compute Service \(ECS\) instances and disks are not deployed in the same zone. In the following example, you can create a StorageClass by deploying the storage-class-csi-topology.yaml file.

    1.  Create the storage-class-topology.yaml file.
    2.  Copy the following content into the file.

        ```
        apiVersion: storage.k8s.io/v1
        kind: StorageClass
        metadata:
            name: alicloud-disk-topology-ssd
        provisioner: diskplugin.csi.alibabacloud.com
        parameters:
            type: cloud_ssd
        reclaimPolicy: Retain
        volumeBindingMode: WaitForFirstConsumer
        allowVolumeExpansion: true
        ```

        |Parameter|Description|
        |---------|-----------|
        |provisioner|Set this parameter to diskplugin.csi.alibabacloud.com. This is a volume plug-in for provisioning cloud disks of Alibaba Cloud.|
        |type|This parameter specifies the disk type. Valid values are cloud\_efficiency, cloud\_ssd, cloud\_essd, and available. If you set this parameter to available, the system attempts to create a disk in the order of Enhanced SSD, SSD, and ultra disk. The system keeps trying until a disk is created. **Note:** For more information about ECS instance types that do not support Enhanced SSDs, see [Block Storage FAQ](/intl.en-US/Block Storage/Block Storage FAQ.md).

Note|
        |reclaimPolicy|The policy for reclaiming a disk. By default, this parameter is set to Delete. You can also set this parameter to Retain.         -   Delete mode: When PVCs are deleted, data in PVs and disks is automatically deleted.
        -   Retain mode: When PVCs are deleted, data in PVs and disks is not deleted. You need to manually delete the data.
If you require high data security, we recommend that you set the Retain mode to avoid data loss caused by user errors. |
        |encrypted|Optional. This parameter specifies whether a created disk is encrypted. By default, this parameter is set to false, which indicates that a created disk is not encrypted.|
        |volumeBindingMode|The WaitForFirstConsumer mode delays the binding and provisioning of a PV until a pod using the PV is created. The pod is assigned to a node, and a disk is created in the zone where the node is deployed before the binding and provisioning of the PV.|
        |allowVolumeExpansion|When this parameter is set to true, the disk can be automatically expanded.|

    3.  Run the following command to create a StorageClass:

        ```
        kubectl apply -f storage-class-topology.yaml
        ```

-   If you create a disk before you create a pod, you can also create a StorageClass by using the following method.
    1.  Create the storage-class-csi.yaml file.
    2.  Copy the following content into the file.

        ```
        apiVersion: storage.k8s.io/v1
        kind: StorageClass
        metadata:
          name: alicloud-disk-ssd-b
        provisioner: diskplugin.csi.alibabacloud.com
        parameters:
          type: cloud_ssd
          regionid: cn-shenzhen
          zoneId: cn-shenzhen-b
        reclaimPolicy: Retain
        allowVolumeExpansion: true
        volumeBindingMode: Immediate
        ```

        |Parameter|Description|
        |---------|-----------|
        |provisioner|Set this parameter to diskplugin.csi.alibabacloud.com. This is a volume plug-in for provisioning cloud disks of Alibaba Cloud.|
        |type|This parameter specifies the disk type. Valid values are cloud\_efficiency, cloud\_ssd, cloud\_essd, and available. If you set this parameter to available, the system attempts to create a disk in the order of Enhanced SSD, SSD, and ultra disk. The system automatically keeps trying until a disk is created. **Note:** For more information about ECS instance types that do not support Enhanced SSDs, see [Block Storage FAQ](/intl.en-US/Block Storage/Block Storage FAQ.md).

Note|
        |regionId|Optional. This parameter specifies the region where a disk is to be created.|
        |zoneId|Optional. This parameter specifies the zone where a disk is to be created.|
        |reclaimPolicy|The policy for reclaiming a disk. By default, this parameter is set to Delete. You can also set this parameter to Retain.         -   Delete mode: When PVCs are deleted, data in PVs and disks is automatically deleted.
        -   Retain mode: When PVCs are deleted, data in PVs and disks is not deleted. You need to manually delete the data.
If you require high data security, we recommend that you set the Retain mode to avoid data loss caused by user errors. |
        |encrypted|Optional. This parameter specifies whether a created disk is encrypted. By default, this parameter is set to false, which indicates that a created disk is not encrypted.|

    3.  Run the following command to create a StorageClass:

        ```
        kubectl apply -f storage-class-csi.yaml
        ```


## Create a PV through a PVC

The following example provides a YAML file for creating a dynamic PV through a PVC.

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
      storage: 25Gi
  storageClassName: alicloud-disk-ssd
```

**Note:** `storage`: the size of a disk to be created. The minimum size is 20 GiB.

## Create an application

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
  clusterIP: None
  selector:
    app: nginx
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
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
        ports:
        - containerPort: 80
          name: web
        volumeMounts:
        - name: pvc-disk
          mountPath: /data
      volumes:
        - name: pvc-disk
          persistentVolumeClaim:
            claimName: disk-pvc
```

