# Provision Alibaba Cloud disks as dynamic PVs

This topic describes how to provision an Alibaba Cloud disk as a dynamic persistent volume \(PV\). You must manually create a StorageClass and set the storageClassName field in a persistent volume claim \(PVC\) to specify the disk type.

Before you provision a dynamic PV, make sure that you have completed the following steps:

-   [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)
-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md)

For more information about StorageClass, see [Configure StorageClasses](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Disk volumes/Configure StorageClasses.md).

## Create a StorageClass

You can create a StorageClass by using the following methods.

-   Create a StorageClass in WaitForFirstConsumer mode.

    For a multi-zone cluster, the WaitForFirstConsumer binding mode fixes the issue where Elastic Compute Service \(ECS\) instances and cloud disks are not deployed in the same zone. In the following example, a StorageClass is created by deploying a file named storage-class-csi-topology.yaml.

    1.  Create a file named storage-class-topology.yaml.
    2.  Copy the following content into the file:

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
        |type|This parameter specifies the disk type. Valid values: cloud\_efficiency, cloud\_ssd, cloud\_essd, and available. If you set this parameter to available, the system attempts to create a disk in the order of Enhanced SSD, SSD, and ultra disk. The system automatically keeps trying until a disk is created. **Note:** For more information about ECS instance types that do not support Enhanced SSDs, see [Elastic Block Storage FAQ](/intl.en-US/Block Storage/Elastic Block Storage FAQ.md).

Note|
        |reclaimPolicy|The policy for reclaiming a cloud disk. By default, this parameter is set to Delete. You can also set this parameter to Retain.         -   Delete mode: When PVCs are deleted, data in the related PVs and cloud disks is automatically deleted.
        -   Retain mode: When PVCs are deleted, data in the related PVs and cloud disks is not deleted. You need to manually delete the data.
If you require higher data security, we recommend that you set the Retain mode to avoid data loss caused by user errors. |
        |encrypted|Optional. This parameter specifies whether a created cloud disk is encrypted. By default, this parameter is set to false, which indicates that a created cloud disk is not encrypted.|
        |volumeBindingMode|The WaitForFirstConsumer mode delays the binding and provisioning of a PV until a pod that uses the PVC is created. Before the PV is bound and provisioned, the pod is scheduled to a node and a cloud disk is created in the zone where the node is deployed.|
        |allowVolumeExpansion|When this parameter is set to true, the cloud disk can be automatically expanded.|
        |performanceLevel|Valid values: PL1, PL2, and PL3. For more information, see [Capacity and PLs of ESSDs](/intl.en-US/Block Storage/Block Storage overview/Enhanced SSDs.md).|

    3.  Run the following command to create a StorageClass:

        ```
        kubectl apply -f storage-class-topology.yaml
        ```

-   If you create a cloud disk before you create a pod, you can also create a StorageClass by using the following method.
    1.  Create a file named storage-class-csi.yaml.
    2.  Paste the following content into the file.

        ```
        apiVersion: storage.k8s.io/v1
        kind: StorageClass
        metadata:
          name: alicloud-disk-ssd-b
        provisioner: diskplugin.csi.alibabacloud.com
        parameters:
          type: cloud_ssd
          regionId: cn-shenzhen
          zoneId: cn-shenzhen-b
        reclaimPolicy: Retain
        allowVolumeExpansion: true
        volumeBindingMode: Immediate
        ```

        |Parameter|Description|
        |---------|-----------|
        |provisioner|Set this parameter to diskplugin.csi.alibabacloud.com. This is a volume plug-in for provisioning cloud disks of Alibaba Cloud.|
        |type|This parameter specifies the disk type. Valid values: cloud\_efficiency, cloud\_ssd, cloud\_essd, and available. If you set this parameter to available, the system attempts to create a disk in the order of Enhanced SSD, SSD, and ultra disk. The system automatically keeps trying until a disk is created. **Note:** For more information about ECS instance types that do not support Enhanced SSDs, see [Elastic Block Storage FAQ](/intl.en-US/Block Storage/Elastic Block Storage FAQ.md).

Note|
        |regionId|Optional. This parameter specifies the region where a cloud disk is to be created.|
        |zoneId|Optional. This parameter specifies the zone where a cloud disk is to be created.|
        |reclaimPolicy|The policy for reclaiming a cloud disk. By default, this parameter is set to Delete. You can also set this parameter to Retain.         -   Delete mode: When PVCs are deleted, data in the related PVs and cloud disks is automatically deleted.
        -   Retain mode: When PVCs are deleted, data in the related PVs and cloud disks is not deleted. You need to manually delete the data.
If you require higher data security, we recommend that you set the Retain mode to avoid data loss caused by user errors. |
        |encrypted|Optional. This parameter specifies whether a created cloud disk is encrypted. By default, this parameter is set to false, which indicates that a created cloud disk is not encrypted.|

    3.  Run the following command to create a StorageClass:

        ```
        kubectl apply -f storage-class-csi.yaml
        ```


## Create a PV through a PVC

The following code block shows how to create a PVC that can be used to create a dynamic PV:

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

**Note:** `storage`: the size of a cloud disk to be created. The minimum size is 20 GiB.

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

