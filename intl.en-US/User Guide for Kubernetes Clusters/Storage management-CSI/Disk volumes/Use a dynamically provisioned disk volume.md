---
keyword: [dynamically provisioned disk volume, CSI, K8s, persistent storage]
---

# Use a dynamically provisioned disk volume

Alibaba Cloud disks are block-level data storage products for Elastic Compute Service \(ECS\). Alibaba Cloud disks provide low latency, high performance, high durability, and high reliability. You can use the Container Storage Interface \(CSI\) plug-in to enable static provisioning and dynamic provisioning of disks. This topic describes how to mount a dynamically provisioned disk volume by using CSI and how to enable persistent storage by using a dynamically provisioned disk volume.

Before you use a dynamically provisioned disk volume, make sure that the following steps are completed:

-   A Container Service for Kubernetes \(ACK\) cluster is created and installed with the CSI plug-in. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   A kubectl client is connected to the created cluster. For more information, see [Connect to Kubernetes clusters by using kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to Kubernetes clusters by using kubectl.md).

Scenarios: If no disk is available when you deploy an application in an ACK cluster, the system automatically purchases a disk and mounts it as a dynamically provisioned volume.

How to use a dynamically provisioned disk volume:

1.  Manually create a persistent volume claim \(PVC\) and specify a StorageClass in the PVC.
2.  When you deploy an application, the system automatically creates a persistent volume \(PV\) based on the specified StorageClass.

For more information about StorageClasses, see [Usage notes for disk volumes](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Disk volumes/Usage notes for disk volumes.md).

## Step 1: Create a StorageClass

By default, four StorageClasses are automatically created in an ACK cluster when the system is initialized. All the four StorageClasses use the default settings. These StorageClasses include:

-   alicloud-disk-common: creates a basic disk.
-   alicloud-disk-efficiency: creates an ultra disk.
-   alicloud-disk-ssd: creates a standard SSD.
-   alicloud-disk-available: a high-availability mode. In this mode, the system attempts to create a standard SSD. If the standard SSD resources are exhausted, the system then attempts to create an ultra disk.

The four StorageClasses are applicable in only single-zone clusters.

You can create a StorageClass by using the following methods based on your requirements.

**Method 1: Create a StorageClass that specifies the WaitForFirstConsumer mode**

You can use the WaitForFirstConsumer mode to mount a disk to an ECS instance in a different zone. In the following example, a StorageClass is created by using a file named storage-class-csi-topology.yaml.

1.  Use the following template to create a file named storage-class-topology.yaml.

    ```
    apiVersion: storage.k8s.io/v1
    kind: StorageClass
    metadata:
        name: alicloud-disk-topology-ssd
    provisioner: diskplugin.csi.alibabacloud.com
    reclaimPolicy: Retain
    parameters:
        type: cloud_ssd
          regionId: cn-hangzhou
          zoneId: cn-hangzhou-b
          fstype: "ext4"
          readonly: "false"
    reclaimPolicy: Retain
    volumeBindingMode: WaitForFirstConsumer
    allowVolumeExpansion: true
    ```

    |Parameter|Description|
    |---------|-----------|
    |name|The name of the StorageClass.|
    |provisioner|Set this parameter to diskplugin.csi.alibabacloud.com. This value specifies that the provisioner of Alibaba Cloud disks is used to create the StorageClass.|
    |reclaimPolicy|The policy for reclaiming the disk. Valid values: Delete and Retain. Default value: Delete. **Note:** If you set the value to Delete, the disk is automatically deleted when you delete the corresponding PVC. The disk data cannot be restored. |
    |type|The disk type. Valid values: cloud\_efficiency, cloud\_ssd, cloud\_essd, and available. If you set this parameter to available, the system attempts to create a disk in the order of enhanced SSD, standard SSD, and ultra disk. The system keeps trying until a disk is created. **Note:** For more information about ECS instance types that do not support enhanced SSDs, see [Elastic Block Storage FAQ](/intl.en-US/Block Storage/Elastic Block Storage FAQ.md).

Note|
    |regionId|The ID of the region where the disk is to be created. The value must be set to the region ID of the cluster. This parameter is optional.|
    |zoneId|The ID of the zone where the disk is to be created. This parameter is optional.     -   If your cluster is deployed in one zone, set the value to the ID of the zone.
    -   If your cluster is deployed across zones, set the value to the IDs of the zones. Example:

        ```
zoneid: cn-hangzhou-a,cn-hangzhou-b,cn-hangzhou-c
        ``` |
    |fstype|The file system of the disk. This parameter is optional. Default value: ext4.|
    |readonly|Specifies whether the disk is read-only. This parameter is optional. Valid values: true and false. true: The disk is read-only. false: You can perform read and write operations on the disk. Default value: false.|
    |reclaimPolicy|The policy for reclaiming a disk. By default, this parameter is set to Delete. You can also set this parameter to Retain.     -   Delete mode: When PVCs are deleted, the related PVs and disks are deleted and the stored data is also deleted.
    -   Retain mode: When PVCs are deleted, data stored in PVs and disks is retained. The data can only be manually deleted.
If you require higher data security, we recommend that you use the Retain mode to avoid data loss caused by user errors. |
    |encrypted|This parameter specifies whether the disk is encrypted. This parameter is optional. Default value: false. This indicates that the disk is not encrypted.|
    |volumeBindingMode|The WaitForFirstConsumer mode delays the binding and provisioning of a PV until a pod that uses the PVC is created. After the pod is scheduled to a node, a disk is created in the zone where the node is deployed and provisioned as the PV.|
    |allowVolumeExpansion|When this parameter is set to true, the disk can be automatically expanded.|
    |performanceLevel|Valid values: PL1, PL2, and PL3. For more information, see [Capacity and PLs](/intl.en-US/Block Storage/Block Storage overview/ESSDs.md).|

2.  Run the following command to create a StorageClass:

    ```
    kubectl apply -f storage-class-topology.yaml
    ```


**Method 2: Create a StorageClass that specifies the WaitForFirstConsumer mode**

1.  Use the following template to create a file named storageclass.yaml.

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
    |provisioner|Set this parameter to diskplugin.csi.alibabacloud.com. This parameter specifies the volume plug-in that is used to provision disks of Alibaba Cloud.|
    |type|The disk type. Valid values: cloud\_efficiency, cloud\_ssd, cloud\_essd, and available. If you set this parameter to available, the system attempts to create a disk in the order of enhanced SSD, standard SSD, and ultra disk. The system automatically keeps trying until a disk is created. **Note:** For more information about ECS instance types that do not support enhanced SSDs, see [Elastic Block Storage FAQ](/intl.en-US/Block Storage/Elastic Block Storage FAQ.md).

Note|
    |regionId|Optional. This parameter specifies the region where the disk is to be created.|
    |zoneId|This parameter is optional. This parameter specifies the zone where the disk is to be created.|
    |reclaimPolicy|The policy for reclaiming the disk. By default, this parameter is set to Delete. You can also set this parameter to Retain.     -   Delete mode: When PVCs are deleted, the related PVs and disks are deleted. The stored data is also deleted.
    -   Retain mode: When PVCs are deleted, the related PVs and disks are retained. The data can only be manually deleted.
If you require higher data security, we recommend that you use the Retain mode to avoid data loss caused by user errors. |
    |encrypted|Optional. This parameter specifies whether the disk is encrypted. Default value: false. This indicates that the disk is not encrypted.|

2.  Run the following command to create a StorageClass:

    ```
    kubectl apply -f storage-class-csi.yaml
    ```


**Verify the result**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Volumes** \> **StorageClasses**.

5.  You can find the created StorageClass on the StorageClasses page.


## Step 2: Create a PVC

1.  Use the following template to create a file named pvc-ssd.yaml.

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

    |Parameter|Description|
    |---------|-----------|
    |`name`|The name of the PVC.|
    |`accessModes`|The access mode of the PVC.|
    |`storageClassName`|The name of the StorageClass that is used to associate with the PVC.|
    |`storage`|The disk size. The minimum capacity is 20 GiB.|

2.  Run the following command to create a PVC:

    ```
    kubectl create -f pvc-ssd.yaml
    ```

3.  View the created PVC.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volume Claims**. You can find the created PVC on the Persistent Volume Claims page.


## Step 3: Deploy an application

1.  Create a file named pvc-dynamic.yaml.

    Use the following template to deploy an application named nginx-dynamic and mount the PVC to the application.

    ```
    apiVersion: apps/v1
    kind: StatefulSet
    metadata:
      name: nginx-dynamic
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
                claimName: disk-ssd
    ```

    -   `mountPath`: the path where the disk is mounted.
    -   `claimName`: the name of the PVC that is mounted to the application.
2.  Run the following command to deploy an application and mount the PVC to the application:

    ```
    kubectl create -f nginx-dynamic.yaml
    ```

    In the left-side navigation pane of the details page, choose **Workloads** \> **StatefulSets**. You can find the created application on the StatefulSets page.


## Verify that the dynamically provisioned disk volume can be used to persist data

1.  View the pod that runs the nginx-dynamic application and the files in the disk.

    1.  Run the following command to query the name of the pod that runs the nginx-dynamic application:

        ```
        kubectl get pod | grep dynamic
        ```

        Expected output:

        ```
        nginx-dynamic-xxx   1/1     Running     0          3m
        ```

    2.  Run the following command to check whether a new disk is mounted to the /data path:

        ```
        kubectl exec nginx-dynamic-xxx df | grep data
        ```

        Expected output:

        ```
        /dev/vdh        20511312    45080  20449848   1% /data
        ```

    3.  Run the following command to query the files in the /data path:

        ```
        kubectl exec nginx-dynamic-xxx ls /data
        ```

        Expected output:

        ```
        lost+found
        ```

2.  Create a file in the disk.

    1.  Run the following command to create a file named dynamic in the /data path:

        ```
        kubectl exec nginx-dynamic-xxx touch /data/dynamic
        ```

    2.  Run the following command to query the files in the /data path:

        ```
        kubectl exec nginx-dynamic-xxx ls /data
        ```

        Expected output:

        ```
        dynamic
        lost+found
        ```

3.  Run the following command to delete the `nginx-dynamic-xxx` pod:

    ```
    kubectl delete pod nginx-dynamic-xxx
    ```

    Expected output:

    ```
    pod "nginx-dynamic-xxx" deleted
    ```

4.  Verify that the file still exists in the disk after the pod is deleted.

    1.  Run the following command to query the name of the recreated pod:

        ```
        kubectl get pod 
        ```

        Expected output:

        ```
        NAME                               READY   STATUS      RESTARTS   AGE
        nginx-dynamic-xxx                   1/1     Running     0          2m
        ```

    2.  Run the following command to query the files in the /data path:

        ```
        kubectl exec nginx-dynamic-xxx ls /data
        ```

        Expected output:

        ```
        dynamic
        lost+found
        ```

        The dynamic file still exists in the /data path. This indicates that data is persisted to the dynamically provisioned disk.


