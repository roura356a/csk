# Use dynamic disks to create PVs for stateful services

This topic describes the common scenarios of dynamic disks and how to use dynamic disks to create persistent volumes \(PVs\) for stateful services.

## Background

When to use dynamic disks:

You have not purchased disks. When you deploy an application, the system automatically purchases a disk.

How to use dynamic disks:

1.  Create a persistent volume claim \(PVC\) and specify the StorageClass in the PVC.
2.  When you deploy an application, the system automatically creates a PV based on the StorageClass.

## Prerequisites

-   A Container Service for Kubernetes \(ACK\) cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   The cluster is connected through kubectl. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).
-   A provisioner is installed for the cluster. The provisioner can automatically create a disk based on the StorageClass.

## Install a provisioner

By default, a provisioner is automatically installed during cluster creation.

## Create a StorageClass

By default, a cluster has four StorageClasses. Each StorageClass uses the default settings. The four StorageClasses apply to a cluster where nodes are deployed in the same zone. If a cluster has nodes running across multiple zones, you must create a StorageClass. The following list describes the four StorageClasses:

-   alicloud-disk-common: creates a basic disk.
-   alicloud-disk-efficiency: creates an ultra disk.
-   alicloud-disk-ssd: creates an SSD.
-   alicloud-disk-available: This option ensures high availability. The system attempts to create an ultra disk first. If no ultra disk is available in the specified zone, the system attempts to create an SSD. If no SSD is available, the system attempts to create a basic disk.

1.  Add the following content to the storageclass.yaml file:

    ```
    kind: StorageClass
    apiVersion: storage.k8s.io/v1beta1
    metadata:
      name: alicloud-disk-ssd-hangzhou-b
    provisioner: alicloud/disk
    reclaimPolicy: Retain
    parameters:
      type: cloud_ssd
      regionid: cn-hangzhou
      zoneid: cn-hangzhou-b
      fstype: "ext4"
      readonly: "false"
    ```

    |Parameter|Description|
    |---------|-----------|
    |`provisioner`|Automatically creates a disk for the cluster. Set the value to alicloud/disk.|
    |`reclaimPolicy`|The policy for reclaiming the disk. Valid values: Delete and Retain. Default value: Delete.**Note:** If you set the value to Delete, the disk is automatically deleted when you delete the PVC. The disk data cannot be restored. |
    |`type`|The type of the disk. Valid values: cloud, cloud\_efficiency, cloud\_ssd, and available.|
    |`regionid`|Optional. The ID of the region to which the disk belongs. Set the value to the region ID of the cluster.|
    |`zoneid`|Optional. The ID of the zone to which the disk belongs.    -   If all nodes in the cluster are running in the same zone, set the value to the zone ID of the cluster.
    -   If the cluster has nodes running across multiple zones, specify multiple zone IDs based on the following example.

        ```
zoneid: cn-hangzhou-a,cn-hangzhou-b,cn-hangzhou-c
        ``` |
    |`fstype`|Optional. The type of the disk file system. Default value: ext4.|
    |`readonly`|Specifies whether the disk is read-only. This parameter is optional. Valid values: true and false. true: The disk is read-only. false: You can perform read and write operations on the disk. Default value: false.|
    |`encrypted`|Specifies whether to encrypt the disk. This parameter is optional. Valid values: true and false. true: encrypts the disk. false: does not encrypt the disk. Default value: false.|

2.  Run the following command to create a StorageClass:

    ```
    kubectl create -f storageclass.yaml
    ```


## Create a PVC

1.  Add the following content to the pvc-ssd.yaml file:

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


In the left-side navigation pane of the management page of the cluster, click **Persistent Volumes**. On the **Persistent Volume Claims** tab, you can view the PVC. The `StorageClass` of the PVC is alicloud-disk-ssd-hangzhou-b, and the specified PV is bound to the PVC.

## Create an application

1.  Add the following content to the pvc-dynamic.yaml file:

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

2.  Run the following command to create an application:

    ```
    kubectl create -f nginx-dynamic.yaml
    ```


In the left-side navigation pane of the details page of the cluster, click **Workload**. On the **Deployments** tab, view the application.

## Use dynamic disks for persistent storage

1.  Run the following command to query the pod that runs the application:

    ```
    kubectl get pod | grep dynamic
    ```

    The following command output appears:

    ```
    nginx-dynamic-5c74594ccb-zl9pf     2/2     Running     0          3m
    ```

2.  Run the following command to check whether a new disk is mounted to the /data path:

    ```
    kubectl exec nginx-dynamic-5c74594ccb-zl9pf df | grep data
    ```

    The following command output appears:

    ```
    /dev/vdh        20511312    45080  20449848   1% /data
    ```

3.  Run the following command to query the files in the /data path:

    ```
    kubectl exec nginx-dynamic-5c74594ccb-zl9pf ls /data
    ```

    The following command output appears:

    ```
    lost+found
    ```

4.  Run the following command to create file dynamic in the /data path:

    ```
    kubectl exec nginx-dynamic-5c74594ccb-zl9pf touch /data/dynamic
    ```

5.  Run the following command to query the files in the /data path:

    ```
    kubectl exec nginx-dynamic-5c74594ccb-zl9pf ls /data
    ```

    The following command output appears:

    ```
    dynamic
    lost+found
    ```

6.  Run the following command to delete pod `nginx-dynamic-5c74594ccb-zl9pf`:

    ```
    kubectl delete pod nginx-dynamic-5c74594ccb-zl9pf
    ```

    The following command output appears:

    ```
    pod "nginx-dynamic-5c74594ccb-zl9pf" deleted
    ```

7.  Open another kubectl CLI and run the following command to query how the pod is deleted and a new pod is created:

    ```
    kubectl get pod -w -l app=nginx
    ```

    The following command output appears:

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

8.  Run the following command to query the new pod:

    ```
    kubectl get pod 
    ```

    The following command output appears:

    ```
    NAME                               READY   STATUS      RESTARTS   AGE
    nginx-dynamic-5c74594ccb-45sd4     2/2     Running     0          2m
    ```

9.  Run the following command to verify that file dynamic in the /data path is not deleted. This indicates that data in the dynamic disk is persistently stored.

    ```
    kubectl exec nginx-dynamic-5c74594ccb-45sd4 ls /data
    ```

    The following command output appears:

    ```
    dynamic
    lost+found
    ```


