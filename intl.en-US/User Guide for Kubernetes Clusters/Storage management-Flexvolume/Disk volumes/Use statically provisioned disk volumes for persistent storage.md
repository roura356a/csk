---
keyword: [statically provisioned disk volume, persistent storage]
---

# Use statically provisioned disk volumes for persistent storage

When a node that hosts running containers fails, stateful applications may lose the business data stored in the containers. This issue can be resolved by using persistent storage. This topic describes how to use a statically provisioned disk volume to persist data.

Before you start, make sure that you have performed the following operations:

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [Create a disk](/intl.en-US/Block Storage/Cloud disks/Create a cloud disk/Create a disk.md)
-   [t16645.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to Kubernetes clusters by using kubectl.md)

Scenarios

-   Provide storage space for applications that require high disk I/O and do not require data sharing. For example, storage services such as MySQL and Redis.
-   Write log data at high speeds.
-   Store data in a way that is independent of the lifetime of a pod.

You must create a disk before you can statically provision a disk volume.

You must manually create a persistent volume \(PV\) and a persistent volume claim \(PVC\) to use a statically provisioned disk volume. For more information, see [Use Alibaba Cloud disks as statically provisioned volumes](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Disk volumes/Use Alibaba Cloud disks as statically provisioned volumes.md).

## Limits

-   The disks provided by Alibaba Cloud cannot be shared. Each disk can be mounted only to one pod.
-   A disk can be mounted only to a node that is deployed in the same zone as the disk.

## Create a PV

1.  Create a file named pv-static.yaml.

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
    -   `failure-domain.beta.kubernetes.io/zone: <your-zone>`: the zone where the disk is deployed. Example: cn-hangzhou-b.
    -   `failure-domain.beta.kubernetes.io/region: <your-region>`: the region where the disk is deployed. Example: cn-hangzhou.
    If your cluster is deployed across zones, you must specify `failure-domain.beta.kubernetes.io/zone` and `failure-domain.beta.kubernetes.io/region`. This ensures that your pods are scheduled to the zone where the disk is deployed.

2.  Run the following command to create a PV:

    ```
    kubectl create -f pv-static.yaml
    ```

    **Verify the result**

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).
    2.  In the left-side navigation pane, click **Clusters**.
    3.  On the Clusters page, find the cluster that you want to manage, and click the name of the cluster or click **Details** in the **Actions** column.
    4.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volumes**. Verify that the newly created PV is displayed.

## Create a PVC

1.  Create a file named pvc-static.yaml.

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

    **View the result**

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).
    2.  In the left-side navigation pane, click **Clusters**.
    3.  On the Clusters page, find the cluster that you want to manage, and click the name of the cluster or click **Details** in the **Actions** column.
    4.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volume Claims**.
    5.  On the **Persistent Volume Claims** page, verify that the newly created PVC is displayed.

## Create an application

1.  Create a file named static.yaml.

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

2.  Run the following command to deploy an application that uses the statically provisioned disk volume:

    ```
    kubectl create -f static.yaml
    ```

    **View the result**

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).
    2.  In the left-side navigation pane, click **Clusters**.
    3.  On the Clusters page, find the cluster that you want to manage, and click the name of the cluster or click **Details** in the **Actions** column.
    4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.
    5.  On the **Deployments** page, verify that the newly created application is displayed.

## Verify data persistence

1.  Run the following command to query the pods that host the application:

    ```
    kubectl get pod | grep static
    ```

    Expected output:

    ```
    nginx-static-78c7dcb9d7-g****   2/2     Running     0          32s
    ```

2.  Run the following command to check whether a new disk is mounted to the /data path:

    ```
    kubectl exec nginx-static-78c7dcb9d7-g**** df | grep data
    ```

    Expected output:

    ```
    /dev/vdf        20511312    45080  20449848   1% /data
    ```

3.  Run the following command to query the files in the /data path:

    ```
    kubectl exec nginx-static-78c7dcb9d7-g**** ls /data
    ```

    Expected output:

    ```
    lost+found
    ```

4.  Run the following command to create a file named static in the /data path:

    ```
    kubectl exec nginx-static-78c7dcb9d7-g**** touch /data/static
    ```

5.  Run the following command to query the files in the /data path:

    ```
    kubectl exec nginx-static-78c7dcb9d7-g**** ls /data
    ```

    Expected output:

    ```
    static
    lost+found
    ```

6.  Run the following command to delete the pod named `nginx-static-78c7dcb9d7-g****`.

    ```
    kubectl delete pod nginx-static-78c7dcb9d7-g****
    ```

    Expected output:

    ```
    pod "nginx-static-78c7dcb9d7-g****" deleted
    ```

7.  Open another kubectl command-line interface \(CLI\) and run the following command to view how the pod is deleted and recreated:

    ```
    kubectl get pod -w -l app=nginx
    ```

    Expected output:

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

8.  Run the following command to query the recreated pod:

    ```
    kubectl get pod
    ```

    Expected output:

    ```
    NAME                            READY   STATUS      RESTARTS   AGE
    nginx-static-78c7dcb9d7-h****   2/2     Running     0          14s
    ```

9.  Run the following command to verify that the static file still exists in the /data path. This indicates that data is persisted to the disk.

    ```
    kubectl exec nginx-static-78c7dcb9d7-h6brd ls /data
    ```

    Expected output:

    ```
    static
    lost+found
    ```


