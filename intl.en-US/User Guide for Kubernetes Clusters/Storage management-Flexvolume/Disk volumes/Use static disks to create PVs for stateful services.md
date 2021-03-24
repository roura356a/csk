# Use static disks to create PVs for stateful services

This topic describes the common scenarios of static disks and how to use static disks to create persistent volumes \(PVs\) for stateful services.

Before you enable auto scaling, make sure that the following operations are complete:

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [Create a disk](/intl.en-US/Block Storage/Cloud disks/Create a cloud disk/Create a disk.md)
-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md)

Scenarios:

-   Create applications that require high disk I/O and do not require shared data, such as MySQL and Redis.
-   Collect logs at high speeds.
-   Store data in a way that is independent of the lifetime of a pod.

Before you use static disks, make sure that you have purchased disks.

To use a static disk, manually create a PV and a persistent volume claim \(PVC\). For more information, see [Use Alibaba Cloud disks as statically provisioned volumes](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Disk volumes/Use static volumes.md).

## Limits

-   Disks that are provided by Alibaba Cloud cannot be shared. Each disk can be mounted to only one pod at a time.
-   Disks can be mounted to only the nodes that are located in the same zone.

## Create a PV

1.  Create the pv-static.yaml file.

    ```
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: <your-disk-id>
      labels:
        alicloud-pvname: <your-disk-id>
        failure-domain.beta.kubernetes.io/zone: <your-zone>
        failure-domain.beta.kubernetes.io/region: <your-region>
    spec:
      capacity:
        storage: 20Gi
      accessModes:
        - ReadWriteOnce
      flexVolume:
        driver: "alicloud/disk"
        fsType: "ext4"
        options:
          volumeId: "<your-disk-id>"
    ```

    **Note:**

    -   `alicloud-pvname: <your-disk-id>`: the name of the PV. Set the value to the disk ID.
    -   `failure-domain.beta.kubernetes.io/zone: <your-zone>`: the zone where the disk is located. For example, cn-hangzhou-b.
    -   `failure-domain.beta.kubernetes.io/region: <your-region>`: the region where the disk is located. For example, cn-hangzhou.
    If your cluster is deployed across multiple zones, specify the `failure-domain.beta.kubernetes.io/zone` and `failure-domain.beta.kubernetes.io/region` parameters to ensure that your pod is scheduled to the zone where the disk is located.

2.  Run the following command to create a PV:

    ```
    kubectl create -f pv-static.yaml
    ```

    **View results**

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).
    2.  In the left-side navigation pane, click **Clusters**.
    3.  On the Clusters page, find the cluster that you want to manage, and click the cluster name or click **Details** in the **Actions** column of the cluster. The Cluster Information page appears.
    4.  In the left-side pane, click **Persistent Volumes**.
    5.  Click the **Persistent Volumes** tab to view the PV that you created.

## Create a PVC

1.  Create the pvc-static.yaml file.

    ```
    kind: PersistentVolumeClaim
    apiVersion: v1
    metadata:
      name: pvc-disk
    spec:
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 20Gi
      selector:
        matchLabels:
          alicloud-pvname: <your-disk-id>
    ```

2.  Run the following command to create a PVC:

    ```
    kubectl create -f pvc-static.yaml
    ```

    **View results**

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).
    2.  In the left-side navigation pane, click **Clusters**.
    3.  On the Clusters page, find the cluster that you want to manage and click **Details** in the **Actions** column of the cluster.
    4.  In the left-side pane, click **Persistent Volumes**.
    5.  Click the **Persistent Volume Claims** tab to view the PVC that you created.

## Create a deployment

1.  Create the static. yaml file.

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx-static
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
                claimName: pvc-disk
    ```

2.  Run the following command to create a deployment:

    ```
    kubectl create -f static.yaml
    ```

    **View results**

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).
    2.  In the left-side navigation pane, click **Clusters**.
    3.  On the Clusters page, find the cluster to which you mounted the NAS file system, and click the cluster name or **Applications** in the **Actions** column.
    4.  In the left-side navigation pane on the page that appears, click **Workload**.
    5.  Click the **Deployments** tab. You can view the old-nginx deployment.

## Use static disks for persistent storage

1.  Run the following command to query the pod that runs the application:

    ```
    kubectl get pod | grep static
    ```

    ```
    nginx-static-78c7dcb9d7-g****   2/2     Running     0          32s
    ```

2.  Run the following command to check whether a new disk is mounted to the /data path:

    ```
    kubectl exec nginx-static-78c7dcb9d7-g**** df | grep data
    ```

    ```
    /dev/vdf        20511312    45080  20449848   1% /data
    ```

3.  Run the following command to view the files in the /data path:

    ```
    kubectl exec nginx-static-78c7dcb9d7-g**** ls /data
    ```

    ```
    lost+found
    ```

4.  Run the following command to create the static file in the /data path:

    ```
    kubectl exec nginx-static-78c7dcb9d7-g**** touch /data/static
    ```

5.  Run the following command to view files in the /data path of each pod:

    ```
    kubectl exec nginx-static-78c7dcb9d7-g**** ls /data
    ```

    ```
    static
    lost+found
    ```

6.  Run the following command to delete the pod named `nginx-static-78c7dcb9d7-g****`.

    ```
    kubectl delete pod nginx-static-78c7dcb9d7-g****
    ```

    ```
    pod "nginx-static-78c7dcb9d7-g****" deleted
    ```

7.  Open another kubectl CLI and run the following command to view the process of how the pod is deleted and a new pod is created in Kubernetes:

    ```
    kubectl get pod -w -l app=nginx
    ```

    ```
    NAME                            READY   STATUS            RESTARTS   AGE
    nginx-static-78c7dcb9d7-g****   2/2     Running           0          50s
    nginx-static-78c7dcb9d7-g****   2/2     Terminating       0          72s
    nginx-static-78c7dcb9d7-h****   0/2     Pending           0          0s
    nginx-static-78c7dcb9d7-h****   0/2     Pending           0          0s
    nginx-static-78c7dcb9d7-h****   0/2     Init:0/1          0          0s
    nginx-static-78c7dcb9d7-g****   0/2     Terminating       0          73s
    nginx-static-78c7dcb9d7-h****   0/2     Init:0/1          0          5s
    nginx-static-78c7dcb9d7-g****   0/2     Terminating       0          78s
    nginx-static-78c7dcb9d7-g****   0/2     Terminating       0          78s
    nginx-static-78c7dcb9d7-h****   0/2     PodInitializing   0          6s
    nginx-static-78c7dcb9d7-h****   2/2     Running           0          8s
    ```

8.  Run the following command to view the newly created pod:

    ```
    kubectl get pod
    ```

    ```
    NAME                            READY   STATUS      RESTARTS   AGE
    nginx-static-78c7dcb9d7-h****   2/2     Running     0          14s
    ```

9.  Run the following command to verify that the static file in the /data path is not deleted. This indicates that data in the static disk is persistently stored.

    ```
    kubectl exec nginx-static-78c7dcb9d7-h6brd ls /data
    ```

    ```
    static
    lost+found
    ```


