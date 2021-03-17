# Mount a NAS file system to store data of a stateful application

This topic describes the scenarios where you can mount an Apsara File Storage NAS \(NAS\) file system to pods and how to mount a NAS file system. After you mount a NAS file system to multiple pods, the pods can share data with each other.

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).
-   A NAS file system is created in the NAS File System console. For more information, see [Mount an NFS file system](). The NAS file system and your Kubernetes cluster must be deployed in the same zone.
-   A mount target is added to the NAS file system. For more information, see [Mount an SMB file system on Windows](). Make sure that the NAS file system and your Kubernetes cluster are connected to the same virtual private cloud \(VPC\).

If a NAS file system is mounted to multiple pods, data in the file system is shared by the pods. The application must be capable of automatically synchronizing application data across pods.

**Note:** You are not allowed to grant permissions to access /directory \(root directory\) of the NAS file system. The user account and user group to which the directory belongs cannot be modified.

Scenarios

-   You want to run applications that require high disk I/O throughput.
-   You need a storage service whose read and write throughput is higher than Object Storage Service \(OSS\).
-   You want to share files across hosts. For example, you want to use a NAS file system as a file server.

Procedure

1.  Create a NAS file system and add a mount target to it.
2.  Manually create a persistent volume \(PV\) and a persistent volume claim \(PVC\).

The following content describes how to create a PV or PVC by using the flexvolume plug-in provided by Alibaba Cloud and then mount a NAS file system.

## Create a PV

1.  Create a pv-nas.yaml file.

    ```
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: pv-nas
      labels:
        alicloud-pvname: pv-nas
    spec:
      capacity:
        storage: 5Gi
      accessModes:
        - ReadWriteMany
      flexVolume:
        driver: "alicloud/nas"
        options:
          server: "***-**.cn-hangzhou.nas.aliyuncs.com"   ////Replace the value with the mount target.
          path: "/k8s1"
          vers: "4.0"
    ```

    |Parameter|Description|
    |---------|-----------|
    |`alicloud-pvname`|The name of the PV.|
    |`server`|The mount target of the NAS file system. To view the mount target, log on to the NAS File System console. Click **File System List** in the left-side navigation pane, find the file system, and then click **Management** in the **Operations** column. Click the Mounting Use tab and view the mount address in the **Mount Target** column.|
    |`path`|The mount directory of the NAS file system. You can specify a subdirectory of a NAS file system. If no subdirectories exist, the system automatically creates a subdirectory.|
    |`vers`|The version number of the Network File System \(NFS\) protocol. This parameter is optional. Valid values: 3 and 4.0. Default value: 3.|
    |`mode`|The access permissions on the mount directory. This parameter is optional. By default, this parameter is left empty.**Note:**

    -   You are not allowed to grant permissions to access the root directory of a NAS file system.
    -   If you set the `mode` parameter for a NAS file system that stores a large amount of data, the process of mounting the NAS file system may be time-consuming or even fail. We recommend that you leave this parameter empty. |

2.  Run the following command to create a PV:

    ```
    kubectl create -f pv-nas.yaml
    ```


**Expected results:**

1.  Log on to the [ACK console](https://cs.console.aliyun.com)[ACK console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane, click **Clusters**.
3.  On the Clusters page, find the cluster that you want to manage. Then, click the name of the cluster or click **Details** in the **Actions** column.
4.  In the left-side pane of the details page, click **Volumes**.
5.  Click the **Persistent Volumes** tab to view the PV that you created.

## Create a PVC

Before you create a PVC for a NAS file system, you must set the `selector` parameter to specify the PV with which the PVC is associated.

1.  Create a pvc-nas.yaml file.

    ```
    kind: PersistentVolumeClaim
    apiVersion: v1
    metadata:
      name: pvc-nas
    spec:
      accessModes:
        - ReadWriteMany
      resources:
        requests:
          storage: 5Gi
      selector:
        matchLabels:
          alicloud-pvname: pv-nas
    ```

2.  Run the following command to create a PVC:

    ```
    kubectl create -f pvc-nas.yaml
    ```


**Expected results:**

1.  Log on to the [ACK console](https://cs.console.aliyun.com)[ACK console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane, click **Clusters**.
3.  On the Clusters page, find the cluster that you want to manage. Then, click the name of the cluster or click **Details** in the **Actions** column.
4.  In the left-side pane of the details page, click **Volumes**.
5.  Click the **Persistent Volume Claims** tab to view the PVC that you created.

## Deploy an application

1.  Create a nas.yaml file.

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nas-static
      labels:
        app: nginx
    spec:
      replicas: 2
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
            volumeMounts:
              - name: pvc-nas
                mountPath: "/data"
          volumes:
            - name: pvc-nas
              persistentVolumeClaim:
                claimName: pvc-nas
    ```

2.  Run the following command to deploy an application:

    ```
    kubectl create -f nas.yaml
    ```


**Expected results:**

1.  Log on to the [ACK console](https://cs.console.aliyun.com)[ACK console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane, click **Clusters**.
3.  On the Clusters page, find the cluster that you want to manage. Then, click the name of the cluster or click **Applications** in the **Actions** column.
4.  In the left-side navigation pane of the details page, click **Workloads**.
5.  On the page that appears, click the **Deployments** tab to view the application that you deployed.

## Verify that the NAS file system is shared by pods

1.  Run the following command to query the pods of the application.

    ```
    kubectl get pod
    ```

    ```
    NAME                             READY   STATUS    RESTARTS   AGE
    nas-static-f96b6b5d7-r****       1/1     Running   0          9m
    nas-static-f96b6b5d7-w****       1/1     Running   0          9m
    ```

2.  Run the following commands to view files in the /data path:

    ```
    kubectl exec nas-static-f96b6b5d7-r**** ls /data                    
    ```

    ```
    kubectl exec nas-static-f96b6b5d7-w**** ls /data                   
    ```

    **Note:** No files are found in the /data path.

3.  Run the following command to create the nas file in the /data path of a pod:

    ```
    kubectl exec nas-static-f96b6b5d7-r**** touch /data/nas
    ```

4.  Run the following commands to view files in the /data path:

    ```
    kubectl exec nas-static-f96b6b5d7-r**** ls /data
    ```

    ```
    nas
    ```

    ```
    kubectl exec nas-static-f96b6b5d7-w**** ls /data
    ```

    ```
    nas
    ```

    **Note:** Create a file in the /data path of a pod. Then, you can find the file in the same path \(/data\) on other pods. This indicates that these pods share the NAS file system.


## Verify that data in the NAS file system is persisted.

1.  Run the following commands to delete all pods of the application:

    ```
    kubectl delete pod nas-static-f96b6b5d7-r**** nas-static-f96b6b5d7-wthmb
    ```

    ```
    pod "nas-static-f96b6b5d7-r****" deleted
    pod "nas-static-f96b6b5d7-w****" deleted
    ```

2.  Run the following command to view how the pods are deleted and recreated:

    ```
    kubectl get pod -w -l app=nginx
    ```

    ```
    NAME                             READY   STATUS              RESTARTS       AGE
    nas-static-f96b6b5d7-r****       1/1     Running             0              27m
    nas-static-f96b6b5d7-w****       1/1     Running             0              27m
    nas-static-f96b6b5d7-r****       1/1     Terminating         0              28m
    nas-static-f96b6b5d7-w****       0/1     Pending             0              0s
    nas-static-f96b6b5d7-w****       0/1     Pending             0              0s
    nas-static-f96b6b5d7-w****       0/1     ContainerCreating   0              0s
    nas-static-f96b6b5d7-w****       1/1     Terminating         0              28m
    nas-static-f96b6b5d7-n****       0/1     Pending             0              0s
    nas-static-f96b6b5d7-n****       0/1     Pending             0              0s
    nas-static-f96b6b5d7-n****       0/1     ContainerCreating   0              0s
    nas-static-f96b6b5d7-r****       0/1     Terminating         0              28m
    nas-static-f96b6b5d7-w****       0/1     Terminating         0              28m
    nas-static-f96b6b5d7-r****       0/1     Terminating         0              28m
    nas-static-f96b6b5d7-r****       0/1     Terminating         0              28m
    nas-static-f96b6b5d7-w****       1/1     Running             0              10s
    nas-static-f96b6b5d7-w****       0/1     Terminating         0              28m
    nas-static-f96b6b5d7-w****       0/1     Terminating         0              28m
    nas-static-f96b6b5d7-n****       1/1     Running             0              17s
    ```

3.  Run the following command to view the names of the recreated pods:

    ```
    kubectl get pod
    ```

    ```
    NAME                             READY   STATUS    RESTARTS   AGE
    nas-static-f96b6b5d7-n****       1/1     Running   0          21s
    nas-static-f96b6b5d7-w****       1/1     Running   0          21s
    ```

4.  Run the following commands to view files in the /data path:

    ```
    kubectl exec nas-static-f96b6b5d7-n**** ls /data
    ```

    ```
    nas
    ```

    ```
    kubectl exec nas-static-f96b6b5d7-w**** ls /data
    ```

    ```
    nas
    ```

    **Note:** The nas file still exists. This indicates that data is persisted to the NAS file system.


