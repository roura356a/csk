---
keyword: [statically provisioned disk, persistent storage, CSI]
---

# Use statically provisioned disks to persist data

When a StatefulSet container malfunctions, the business data stored in the container may be corrupted or even lost. This issue can be resolved by using persistent storage. This topic describes how to use a statically provisioned disk to persist data.

-   [Create a disk](/intl.en-US/Block Storage/Cloud disks/Create a cloud disk/Create a disk.md)
-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)

Scenarios:

-   Create applications that require high disk I/O and do not require data sharing. In this case, the applications can use storage services such as ApsaraDB RDS for MySQL and ApsaraDB for Redis.
-   Collect log data at high speeds.
-   Persist data in a way that is independent of the lifecycle of pods.

To mount a disk as a statically provisioned volume, make sure that you have purchased a disk.

Manually create a persistent volume \(PV\) and a persistent volume claim \(PVC\) to statically provision the disk. For more information, see [Use Alibaba Cloud disks as statically provisioned volumes](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Disk volumes/Use static volumes.md).

## Limits

-   Disks provided by Alibaba Cloud cannot be shared. Each disk can be mounted to only one pod at a time.
-   Disks can be mounted to only nodes that are deployed in the zone where the disks are created.

## Step 1: Create a PV

1.  Use the following template to create a file named pv-static.yaml:

    ```
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: csi-pv
      labels:
        alicloud-pvname: static-disk-pv
    spec:
      capacity:
        storage: 25Gi
      accessModes:
        - ReadWriteOnce
      persistentVolumeReclaimPolicy: Retain
      csi:
        driver: diskplugin.csi.alibabacloud.com
        volumeHandle: "<your-disk-id>"
      nodeAffinity:
        required:
          nodeSelectorTerms:
          - matchExpressions:
            - key: topology.diskplugin.csi.alibabacloud.com/zone
              operator: In
              values:
              - "<your-node-zone-id>"
    ```

    |Parameter|Description|
    |---------|-----------|
    |`name`|The name of the PV.|
    |`labels`|The labels that are added to the PV.|
    |`storage`|The available storage of the disk.|
    |`accessModes`|The access mode of the PV.|
    |`persistentVolumeReclaimPolicy`|The policy for reclaiming the PV.|
    |`driver`|The type of driver. This parameter is set to diskplugin.csi.alibabacloud.com. The value indicates that the Alibaba Cloud CSI plug-in is used.|
    |`volumeHandle`|The ID of the disk.|
    |`nodeAffinity`|The zone to which the PV and PVC belong. You can set this parameter to specify the zone to which the pod of the PV and PVC is scheduled.|

2.  Run the following command to create a PV:

    ```
    kubectl create -f pv-static.yaml
    ```

    Log on to the [ACK console](https://cs.console.aliyun.com). In the left-side navigation pane, click **Clusters**. On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Details** in the **Actions** column. The details page of the cluster appears. In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volumes**. You can view the created PV on the Persistent Volumes page.


## Step 2: Create a PVC

1.  Use the following template to create a file named pvc-static.yaml:

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: csi-pvc
    spec:
      accessModes:
      - ReadWriteOnce
      resources:
        requests:
          storage: 25Gi
      selector:
        matchLabels:
          alicloud-pvname: static-disk-pv
    ```

    |Parameter|Description|
    |---------|-----------|
    |`name`|The name of the PVC.|
    |`accessModes`|The access mode of the PVC.|
    |`storage`|The storage that is required by the application. The storage cannot exceed the capacity of the PV.|
    |`matchLabels`|The labels that are used to associate the PVC with the PV. The labels must be the same as those of the PV.|

2.  Run the following command to create a PVC:

    ```
    kubectl create -f pvc-static.yaml
    ```

    In the left-side navigation pane, choose **Volume** \> **Persistent Volume Claims**. On the Persistent Volume Claims page, you can view the created PVC.


## Step 3: Deploy an application

Deploy an application named nginx-static and mount the PVC to the application. In this example, an NGINX application is created.

1.  Use the following template to create a file named static.yaml.

    Deploy an application named nginx-static and mount the PVC to the application.

    ```
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
                claimName: csi-pvc
    ```

    -   `mountPath`: the path where the disk is mounted in the container.
    -   `claimName`: the name of the PVC, which is used to associate the application with the PVC.
2.  Run the following command to deploy the NGINX application and mount the PVC to the application:

    ```
    kubectl create -f static.yaml
    ```

    In the left-side navigation pane of the details page, choose **Workloads** \> **StatefulSets**. You can view the created application on the StatefulSets page.


## Verify that the statically provisioned disk can be used to persist data

1.  View the pod where the nginx-static application is deployed and the file in the disk.

    1.  Run the following command to view the pod where the nginx-static application is deployed:

        ```
        kubectl get pod | grep static
        ```

        Expected output:

        ```
        nginx-xxxxxx   1/1     Running     0          32s
        ```

    2.  Run the following command to check whether a new disk is mounted to the /data path:

        ```
        kubectl exec nginx-xxxxxx df | grep data
        ```

        Expected output:

        ```
        /dev/vdf        20511312    45080  20449848   1% /data
        ```

    3.  Run the following command to view the file in the /data path:

        ```
        kubectl exec nginx-xxxxxx ls /data
        ```

        Expected output:

        ```
        lost+found
        ```

2.  Run the following command to create a file named static in the /data path:

    ```
    kubectl exec nginx-xxxxxx touch /data/static
    ```

3.  Run the following command to view the file in the /data path:

    ```
     kubectl exec nginx-xxxxxx ls /data
    ```

    Expected output:

    ```
    static
    lost+found
    ```

4.  Run the following command to delete the `nginx-xxxxxx` pod:

    ```
    kubectl delete pod nginx-xxxxxx
    ```

    Expected output:

    ```
    pod "nginx-xxxxxx" deleted
    ```

5.  Verify that the file still exists in the disk after the pod is deleted.

    1.  Run the following command to view the pod that is recreated.

        ```
        kubectl get pod
        ```

        Expected output:

        ```
        NAME                            READY   STATUS      RESTARTS   AGE
        nginx-xxxxxx                    1/1     Running     0          14s
        ```

    2.  Run the following command to view the file in the /data path:

        ```
        kubectl exec nginx-xxxxxx ls /data
        ```

        Expected output:

        ```
        static
        lost+found
        ```

        The static file still exists in the disk. This indicates that data is persisted to the statically provisioned disk.


