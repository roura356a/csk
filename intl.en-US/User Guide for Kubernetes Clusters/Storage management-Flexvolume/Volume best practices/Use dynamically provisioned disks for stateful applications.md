# Use dynamically provisioned disks for stateful applications

This topic describes when and how to use dynamically provisioned disks for stateful applications.

## Background information

When to use a dynamically provisioned disk:

If no disk is available when you deploy an application in a Container Service for Kubernetes \(ACK\) cluster, the system automatically creates a disk and uses it as a dynamic volume.

How to use a dynamically provisioned disk:

1.  Manually create a persistent volume claim \(PVC\) and specify a StorageClass in the PVC.
2.  When you deploy an application, the system automatically creates a persistent volume \(PV\) of the specified StorageClass.

## Prerequisites

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   You are connected to the cluster through kubectl. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).
-   A provisioner is installed for the cluster. The provisioner can automatically create a disk of the specified StorageClass.

## Provisioner

By default, a provisioner is automatically installed in each ACK cluster.

## Create a StorageClass

By default, ACK creates four StorageClasses when the system is initialized. All of the StorageClasses use the default settings. Only single-zone clusters can use the four StorageClasses to dynamically provision disks. If your cluster is deployed in more than one zone, you must create new StorageClasses to mount disks as dynamic volumes. The following list describes the four StorageClasses:

-   alicloud-disk-common: creates a basic disk.
-   alicloud-disk-efficiency: creates an ultra disk.
-   alicloud-disk-ssd: creates an SSD.
-   alicloud-disk-available: a high-availability mode. In this mode, the system attempts to create an SSD. If SSD resources are exhausted, the system then attempts to create an ultra disk.

1.  Create a storageclass.yaml file.

    ```
    kind: StorageClass
    apiVersion: storage.k8s.io/v1beta1
    metadata:
      name: alicloud-disk-ssd-hangzhou-b
    provisioner: alicloud/disk
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
    |`provisioner`|The value is set to alicloud/disk to automatically create a disk by using the provisioner.|
    |`reclaimPolicy`|Specifies how the disk is handled after the corresponding PVC is deleted. Valid values: Delete and Retain. Default value: Delete.**Note:** If you set the value to Delete, the disk is automatically deleted when you delete the PVC. The disk data cannot be restored. |
    |`type`|The type of disk. Valid values: cloud, cloud\_efficiency, cloud\_ssd, and available.|
    |`regionId`|The ID of the region to which the disk belongs. The value must be set to the region ID of the cluster. This parameter is optional.|
    |`zoneId`|The ID of the zone to which the disk belongs. This parameter is optional.    -   If your cluster is deployed in one zone, set the value to the ID of the zone.
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


## Create a PVC

1.  Create a pvc-ssd.yaml file.

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

2.  Run the following command to create a PVC:

    ```
    kubectl create -f pvc-ssd.yaml
    ```


On the details page of the cluster, click **Volumes** and choose **Persistent Volume Claims** in the left-side navigation pane. On the Persistent Volume Claims page, you can view the created PVC and the PV that is bound to the PVC. The `StorageClass` of the PVC is alicloud-disk-ssd-hangzhou-b.

## Create a Deployment

1.  Create a pvc-dynamic.yaml file.

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx-dynamic
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
            image: nginx
            volumeMounts:
              - name: disk-pvc
                mountPath: "/data"
          volumes:
            - name: disk-pvc
              persistentVolumeClaim:
                claimName: disk-ssd
    ```

2.  Run the following command to create a Deployment:

    ```
    kubectl create -f nginx-dynamic.yaml
    ```


On the details page of the cluster, click **Workloads** and choose **Deployments** in the left-side navigation pane. On the Deployments page, you can view the created Deployment.

## Use the disk for persistent storage

1.  Run the following command to query the pod that hosts the Deployment:

    ```
    kubectl get pod | grep dynamic
    ```

    The following output is returned:

    ```
    nginx-dynamic-5c74594ccb-zl9pf     2/2     Running     0          3m
    ```

2.  Run the following command to check whether a new disk is mounted to the /data path:

    ```
    kubectl exec nginx-dynamic-5c74594ccb-zl9pf df | grep data
    ```

    The following output is returned:

    ```
    /dev/vdh        20511312    45080  20449848   1% /data
    ```

3.  Run the following command to query the files in the /data path:

    ```
    kubectl exec nginx-dynamic-5c74594ccb-zl9pf ls /data
    ```

    The following output is returned:

    ```
    lost+found
    ```

4.  Run the following command to create the dynamic file in the /data path:

    ```
    kubectl exec nginx-dynamic-5c74594ccb-zl9pf touch /data/dynamic
    ```

5.  Run the following command to query the files in the /data path:

    ```
    kubectl exec nginx-dynamic-5c74594ccb-zl9pf ls /data
    ```

    The following output is returned:

    ```
    dynamic
    lost+found
    ```

6.  Run the following command to delete the pod `nginx-dynamic-5c74594ccb-zl9pf`:

    ```
    kubectl delete pod nginx-dynamic-5c74594ccb-zl9pf
    ```

    The following output is returned:

    ```
    pod "nginx-dynamic-5c74594ccb-zl9pf" deleted
    ```

7.  Open another kubectl command-line interface \(CLI\) and run the following command to query how the pod is deleted and recreated:

    ```
    kubectl get pod -w -l app=nginx
    ```

    The following output is returned:

    ```
    NAME                               READY   STATUS    RESTARTS   AGE
    nginx-dynamic-5c74594ccb-zl9pf     2/2     Running   0          6m48s
    nginx-dynamic-5c74594ccb-zl9pf   2/2   Terminating   0     7m32s
    nginx-dynamic-5c74594ccb-45sd4   0/2   Pending   0     0s
    nginx-dynamic-5c74594ccb-45sd4   0/2   Pending   0     0s
    nginx-dynamic-5c74594ccb-45sd4   0/2   Init:0/1   0     0s
    nginx-dynamic-5c74594ccb-zl9pf   0/2   Terminating   0     7m32s
    nginx-dynamic-5c74594ccb-zl9pf   0/2   Terminating   0     7m33s
    nginx-dynamic-5c74594ccb-zl9pf   0/2   Terminating   0     7m33s
    nginx-dynamic-5c74594ccb-45sd4   0/2   PodInitializing   0     5s
    nginx-dynamic-5c74594ccb-45sd4   2/2   Running   0     22s
    ```

8.  Run the following command to query the name of the recreated pod:

    ```
    kubectl get pod 
    ```

    The following output is returned:

    ```
    NAME                               READY   STATUS      RESTARTS   AGE
    nginx-dynamic-5c74594ccb-45sd4     2/2     Running     0          2m
    ```

9.  Run the following command to verify that the dynamic file in the /data path is not deleted. This indicates that the disk data is persistently stored.

    ```
    kubectl exec nginx-dynamic-5c74594ccb-45sd4 ls /data
    ```

    The following output is returned:

    ```
    dynamic
    lost+found
    ```


