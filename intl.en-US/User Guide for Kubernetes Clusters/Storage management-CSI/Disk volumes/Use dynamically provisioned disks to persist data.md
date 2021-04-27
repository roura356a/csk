---
keyword: [dynamically provisioned disk, persistent storage, CSI]
---

# Use dynamically provisioned disks to persist data

When a StatefulSet container malfunctions, the business data stored in the container may be corrupted or even lost. This issue can be resolved by using persistent storage. This topic describes how to use a dynamically provisioned disk to persist data.

[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)

**Note:** When you create a Container Service for Kubernetes \(ACK\) cluster, the provisioner plug-in is installed by default. The provisioner plug-in automatically creates disks based on StorageClass.

Scenarios: If no disk is available when you deploy an application in an ACK cluster, the system automatically creates a disk and uses it as a dynamic volume.

How to use dynamically provisioned disks:

1.  Manually create a persistent volume claim \(PVC\) and specify a StorageClass in the PVC.
2.  When you deploy an application, the system automatically creates a persistent volume \(PV\) of the specified StorageClass.

## Step 1: Create a StorageClass

By default, four StorageClasses are created in an ACK cluster when the system is initialized. All of the four StorageClasses use the default settings. Only single-zone clusters can use the four StorageClasses to dynamically provision disks. If your cluster is deployed in more than one zone, you must create new StorageClasses to mount disks as dynamic volumes. The following list describes the four StorageClasses:

-   alicloud-disk-common: creates a basic disk.
-   alicloud-disk-efficiency: creates an ultra disk.
-   alicloud-disk-ssd: creates a standard SSD.
-   alicloud-disk-available: a high-availability mode. In this mode, the system attempts to create an SSD. If the SSD resources are exhausted, the system then attempts to create an ultra disk.

1.  Use the following template to create a file named storageclass.yaml.

    ```
    kind: StorageClass
    apiVersion: storage.k8s.io/v1beta1
    metadata:
      name: alicloud-disk-ssd-hangzhou-b
    provisioner: diskplugin.csi.alibabacloud.com
    reclaimPolicy: Retain
    parameters:
      type: cloud_ssd
      regionId: cn-hangzhou
      zoneId: cn-hangzhou-b
      fstype: "ext4"
      readonly: "false"
    ```

    |Parameter|Description|
    |---------|-----------|
    |`name`|The name of the StorageClass.|
    |`provisioner`|The value is set to alicloud/disk to automatically create a disk by using the provisioner plug-in.|
    |`reclaimPolicy`|The policy for reclaiming the disk. Valid values: Delete and Retain. Default value: Delete. **Note:** If you set the value to Delete, the disk is automatically deleted when you delete the PVC. The disk data cannot be restored. |
    |`type`|The type of the disk. Valid values: cloud, cloud\_efficiency, cloud\_ssd, and available.|
    |`regionId`|The ID of the region to which the disk belongs. The value must be set to the region ID of the cluster. This parameter is optional.|
    |`zoneId`|The ID of the zone to which the disk belongs. This parameter is optional.     -   If your cluster is deployed in one zone, set the value to the ID of the zone.
    -   If your cluster is deployed in multiple zones, set the value to the IDs of the zones. Example:

        ```
zoneid: cn-hangzhou-a,cn-hangzhou-b,cn-hangzhou-c
        ``` |
    |`fstype`|The file system of the disk. This parameter is optional. Default value: ext4.|
    |`readonly`|Specifies whether the disk is read-only. This parameter is optional. Valid values: true and false. true: The disk is read-only. false: You can perform read and write operations on the disk. Default value: false.|
    |`encrypted`|Specifies whether to encrypt the disk. This parameter is optional. Valid values: true and false. true: encrypts the disk. false: does not encrypt the disk. Default value: false.|

2.  Run the following command to create a StorageClass:

    ```
    kubectl create -f storageclass.yaml
    ```

3.  View the created StorageClass.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  In the left-side navigation pane of the details page, choose **Volumes** \> **Storage Classes**. You can view the created StorageClass on the Storage Classes page.


## Step 2: Create a PVC

1.  Use the following template to create a file named pvc-ssd.yaml.

    ```
    kind: PersistentVolumeClaim
    apiVersion: v1
    metadata:
      name: disk-ssd
    spec:
      accessModes:
        - ReadWriteOnce
      storageClassName: alicloud-disk-ssd-hangzhou-b
      resources:
        requests:
          storage: 20Gi
    ```

    |Parameter|Description|
    |---------|-----------|
    |`name`|The name of the PVC.|
    |`accessModes`|The access mode of the PVC.|
    |`storageClassName`|The name of the StorageClass, which is used to associate the PVC with the StorageClass.|
    |`storage`|The storage that is required by the application.|

2.  Run the following command to create a PVC:

    ```
    kubectl create -f pvc-ssd.yaml
    ```

3.  View the created PVC.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volume Claims**. You can view the created PVC on the Persistent Volume Claims page.


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
    -   `claimName`: the name of the PVC, which is used to associate the application with the PVC.
2.  Run the following command to deploy an application and mount the PVC to the application.

    ```
    kubectl create -f nginx-dynamic.yaml
    ```

    In the left-side navigation pane of the details page, choose **Workloads** \> **StatefulSets**. You can view the created application on the StatefulSets page.


## Verify that the dynamically provisioned disk can be used to persist data

1.  View the pod where the nginx-dynamic application is deployed and the disk file in the disk.

    1.  Run the following command to view the pod where the nginx-dynamic application is deployed:

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

    3.  Run the following command to view the file in the /data path:

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

    2.  Run the following command to view the file in the /data path:

        ```
        kubectl exec nginx-dynamic-xxx ls /data
        ```

        Expected output:

        ```
        dynamic
        lost+found
        ```

3.  Run the following command to delete the `nginx-dynamic-xxx` pod.

    ```
    kubectl delete pod nginx-dynamic-xxx
    ```

    Expected output:

    ```
    pod "nginx-dynamic-xxx" deleted
    ```

4.  Verify that the file still exists in the disk after the pod is deleted.

    1.  Run the following command to view the pod that is recreated.

        ```
        kubectl get pod 
        ```

        Expected output:

        ```
        NAME                               READY   STATUS      RESTARTS   AGE
        nginx-dynamic-xxx                   1/1     Running     0          2m
        ```

    2.  Run the following command to view the file in the /data path:

        ```
        kubectl exec nginx-dynamic-xxx ls /data
        ```

        Expected output:

        ```
        dynamic
        lost+found
        ```

        The dynamic file still exists in the /data path. This indicates that the data is persisted to the dynamically provisioned disk.


