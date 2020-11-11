# Extend a disk used in a stateful service

This topic describes how to extend a disk that is used in a stateful service.

## Background

When you use a cloud disk to create a stateful service, you may encounter the following problems: when you first request a cloud disk, you cannot accurately estimate the storage space that may be used by the service. The size of the requested disk may be insufficient after the service has been running for a period of time.

You can extend the disk to meet the actual needs.

## Prerequisites

-   You have created a Kubernetes cluster. For more information, see [t16639.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).
-   You can use kubectl to connect to the Kubernetes cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).
-   You have created a PVC and bound it to the automatically created disk. For more information, see [t80612.md\#section\_rjp\_kdz\_dgb](/intl.en-US/Best Practices/Storage/Use dynamic disks to create PVs for stateful services.md) and [t80612.md\#section\_sy5\_fgz\_dgb](/intl.en-US/Best Practices/Storage/Use dynamic disks to create PVs for stateful services.md).
-   You have manually created a disk snapshot.

## Create an application

1.  Create an nginx-dynamic.yaml file with the following content. For more information, see [t80612.md\#section\_fbc\_wgz\_dgb](/intl.en-US/Best Practices/Storage/Use dynamic disks to create PVs for stateful services.md).

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

2.  Run the following command to create a Deployment.

    ```
    $ kubectl create -f nginx-dynamic.yaml
    deployment.apps/nginx-dynamic created
    ```

3.  Run the following command to view the Pod that runs the application.

    ```
    $ kubectl get pod 
    NAME                             READY   STATUS    RESTARTS   AGE
    nginx-dynamic-5b4bdb64c4-f7jwv   1/1     Running   0          1m
    ```

4.  Run the following command to view the disk that the application is using.

    ```
    $ kubectl get pv
    NAME                     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM              STORAGECLASS                   REASON   AGE
    d-bp18weq7be93l9iaobe6   20Gi       RWO            Retain           Bound    default/disk-ssd   alicloud-disk-ssd-hangzhou-g            2m
    ```

5.  Run the following command to check whether a new disk is mounted to the /data path:

    ```
    $ kubectl exec nginx-dynamic-5b4bdb64c4-f7jwv df | grep data
    /dev/vdc        20511312  45080  20449848   1% /data
    ```

6.  Run the following command to view the files under the /data path:

    ```
    $ kubectl exec nginx-dynamic-5b4bdb64c4-f7jwv ls /data
    lost+found
    ```

7.  Run the following command to create a file named disk under the /data path:

    ```
    $ kubectl exec nginx-dynamic-5b4bdb64c4-f7jwv touch /data/disk
    ```

8.  Run the following command to view the files under the /data path:

    ```
    $ kubectl exec nginx-dynamic-5b4bdb64c4-f7jwv ls /data
    disk
    lost+found
    ```


## Unmount the disk

Run the following command to delete the application and unmount the disk.

```
$ kubectl delete deploy nginx-dynamic
deployment.extensions "nginx-dynamic" deleted
```

**Result**

Log on to the ECS console. In the left-side navigation pane, choose **Storage & Snapshots** \> **Disks**. Search by **disk ID** to find the target disk. The disk **Status** is Unattached, indicating that the disk is unmounted.

![](../images/p34622.png)

## Extend the disk

1.  Log on to the [ECS console](https://ecs.console.aliyun.com/#/home).
2.  In the left-side navigation pane, choose **Storage & Snapshots** \> **Disks** to go to the Disks page.
3.  Search by **disk ID** to find the target disk and choose **More** \> **Resize Disk** in the **Actions** column.

    **Note:** For more information, see [Resize partitions and file systems of Linux data disks](/intl.en-US/Best Practices/Block Storage/Resize partitions and file systems of Linux data disks.md).

    ![](../images/p34609.png)

4.  Extend a disk from 20 GB to 30 GB.

    ![](../images/p34612.png)


**Result**

On the Disks page, search by **disk ID** to find the target disk. The **Disk Category \(All\)** shows that the disk size is 30 GB.

![](../images/p34619.png)

## Extend the file system

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).
2.  In the left-side navigation pane, choose **Clusters** \> **Nodes** to go to the Nodes page.
3.  Select the target cluster and mount the extended disk to a worker node. Click the ID of the worker node.
4.  In the left-side navigation pane, click **Disks**. On the Disks page, click **Create Disk** in the upper-right corner. For more information, see [Attach a data disk](/intl.en-US/Block Storage/Cloud disks/Attach a data disk.md).

    ![](../images/p34624.png)

5.  On the Mount Disk page, select the target disk.

    ![](../images/p34625.png)

6.  On the Disks page, choose the target disk and choose **More** \> **Modify Disk Property** in the **Actions** column to view **Device Name**.

    ![](../images/p34626.png)

7.  Log on to the worker node where the target disk is mounted.
8.  Run the following command to view the file system.

    ```
    $ e2fsck -f /dev/vdc
    ```

9.  Run the following command to resize the file system.

    ```
    $ resize2fs /dev/vdc
    ```


## Create an application

1.  Create an nginx-dynamic.yaml file with the following content.

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

2.  Run the following command to create a Deployment.

    ```
    $ kubectl create -f nginx-dynamic.yaml
    deployment.apps/nginx-dynamic created
    ```

3.  Run the following command to view the Pod that runs the Deployment.

    ```
    $ kubectl get pod | grep static
    nginx-dynamic-5b4bdb64c4-gxqs5   0/1       ContainerCreating   0          15s
    ```

4.  Run the following command to verify that the disk is extended to the previously specified size.

    ```
    $ kubectl exec nginx-dynamic-5b4bdb64c4-gxqs5 df | grep data
    /dev/vdc        30832548  45036  30771128   1% /data
    ```

5.  Run the following command to view the content under the /data path. The content is the same before and after disk extension.

    ```
    $ kubectl exec nginx-dynamic-5b4bdb64c4-gxqs5 ls /data
    disk
    lost+found
    ```


