# Provision Alibaba Cloud disks as dynamic volumes

This topic describes how to provision an Alibaba Cloud disk as a dynamic persistent volume \(PV\). You must manually create a StorageClass and set the storageClassName field in a persistent volume claim \(PVC\) to specify the disk type.

-   [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md)
-   [Use kubectl to connect to an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Cluster management/Manage and access clusters/Use kubectl to connect to an ASK cluster.md)

For more information about StorageClasses, see [Configure StorageClasses](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Disk volumes/Configure StorageClasses.md).

## Create a StorageClass.

You can create a StorageClass by using the following methods:

-   Create a StorageClass in WaitForFirstConsumer mode.

    For a multi-zone cluster, the WaitForFirstConsumer binding mode fixes the issue that Elastic Compute Service \(ECS\) instances and disks are not deployed in the same zone. In the following example, you can create a StorageClass by deploying the storage-class-csi-topology.yaml file.

    1.  Create a file named storage-class-topology.yaml.
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
        |type|This parameter specifies the disk type. Valid values: cloud\_efficiency, cloud\_ssd, cloud\_essd, and available. If you set this parameter to available, the system attempts to create a disk in the order of enhanced SSD, SSD, and ultra disk. The system automatically keeps trying until a disk is created. **Note:** For more information about ECS instance types that do not support enhanced SSDs, see [Elastic Block Storage FAQ](/intl.en-US/Block Storage/Elastic Block Storage FAQ.md).

Note|
        |reclaimPolicy|The policy for reclaiming a disk. By default, this parameter is set to Delete. You can also set this parameter to Retain.         -   Delete mode: When PVCs are deleted, data in related PVs and disks is automatically deleted.
        -   Retain mode: When PVCs are deleted, data in related PVs and disks is not deleted. You need to manually delete the data.
If you require higher data security, we recommend that you set the Retain mode to avoid data loss caused by user errors. |
        |encrypted|Optional. This parameter specifies whether a created disk is encrypted. By default, this parameter is set to false, which indicates that a created disk is not encrypted.|
        |volumeBindingMode|The WaitForFirstConsumer mode delays the binding and provisioning of a PV until a pod that uses the PVC is created. Such a pod is scheduled to a node and a disk is created in the zone where the node is deployed before the PV is bound and provisioned.|
        |allowVolumeExpansion|When this parameter is set to true, the disk can be automatically expanded.|

    3.  Run the following command to create a StorageClass:

        ```
        kubectl apply -f storage-class-topology.yaml
        ```

-   If you create a disk before you create a pod, you can also create a StorageClass by using the following method:
    1.  Create a file named storage-class-csi.yaml.
    2.  Copy the following content into the file.

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
        |type|This parameter specifies the disk type. Valid values: cloud\_efficiency, cloud\_ssd, cloud\_essd, and available. If you set this parameter to available, the system attempts to create a disk in the order of enhanced SSD, SSD, and ultra disk. The system automatically keeps trying until a disk is created. **Note:** For more information about ECS instance types that do not support enhanced SSDs, see [Elastic Block Storage FAQ](/intl.en-US/Block Storage/Elastic Block Storage FAQ.md).

Note|
        |regionid|Optional. This parameter specifies the region where a disk is to be created.|
        |zoneid|Optional. This parameter specifies the zone where a disk is to be created.|
        |reclaimPolicy|The policy for reclaiming a disk. By default, this parameter is set to Delete. You can also set this parameter to Retain.         -   Delete mode: When PVCs are deleted, data in related PVs and disks is automatically deleted.
        -   Retain mode: When PVCs are deleted, data in related PVs and disks is not deleted. You need to manually delete the data.
If you require higher data security, we recommend that you set the Retain mode to avoid data loss caused by user errors. |
        |encrypted|Optional. This parameter specifies whether a created disk is encrypted. By default, this parameter is set to false, which indicates that a created disk is not encrypted.|

    3.  Run the following command to create a StorageClass:

        ```
        kubectl apply -f storage-class-csi.yaml
        ```


## Create a PVC

The following code block shows how to create a PVC that can be used to create a dynamic PV.

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

**Note:** `storage`: the size of a disk to be created. You must specify at least 20 GiB.

## Create a Service and an application that uses the created PVC.

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

