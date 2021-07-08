---
keyword: [NAS, shared storage, persistent storage]
---

# Use NAS volumes for shared persistent storage

You can use an Apsara File Storage NAS \(NAS\) volume to persist data and share the data among multiple pods. This topic describes how to use a NAS file system to persist and share data.

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   [t16645.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to Kubernetes clusters by using kubectl.md).
-   A NAS file system is created in the NAS File System console. For more information, see [Mount an NFS file system](). The NAS file system and the cluster are deployed in the same zone.
-   A mount target is added to the NAS file system. For more information, see . The NAS file system and the cluster are deployed in the same virtual private cloud \(VPC\).

If a NAS file system is mounted on multiple pods, the data in the file system is shared among the pods. The application must be able to synchronize data across all pods when data modifications are made by multiple pods.

Scenarios:

-   Your application requires high disk I/O.
-   You need a storage service that offers higher read and write throughput than Object Storage Service \(OSS\).
-   You want to share files across hosts. For example, you want to use a NAS file system as a file server.

Procedure

1.  Create a NAS file system and create a mount target.
2.  Create a persistent volume \(PV\) and a persistent volume claim \(PVC\).

The following section describes how to create a PV and a PVC by using the FlexVolume plug-in provided by Alibaba Cloud and then mount a NAS file system.

## Create a PV

1.  Create a file named pv-nas.yaml.

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
          server: "***-**.cn-hangzhou.nas.aliyuncs.com"   #Replace the value with the mount target.
          path: "/k8s1"
          vers: "4.0"
    ```

    |Parameter|Description|
    |---------|-----------|
    |`alicloud-pvname`|The name of the PV.|
    |`server`|The mount target of the NAS file system. To obtain the mount target, log on to the NAS File System console. In the left-side navigation pane, click **File System List**, find the created file system, and then click **Management** in the **Operations** column. Click the Mounting Use tab and copy the mount address in the **Mount Target** column.|
    |`path`|The mounted directory of the NAS file system. You can specify a subdirectory of a NAS file system. If no subdirectories exist, the system automatically creates a subdirectory.|
    |`vers`|The version number of the Network File System \(NFS\) protocol. This parameter is optional. Valid values: 3 and 4.0. Default value: 3.|
    |`mode`|The access permissions on the mounted directory. This parameter is optional. By default, this parameter is left empty. **Note:**

    -   You are not allowed to grant permissions to access the root directory of a NAS file system.
    -   If you set the `mode` parameter for a NAS file system that stores a large amount of data, the process of mounting the NAS file system may be time-consuming or even fail. We recommend that you leave this parameter empty. |

2.  Run the following command to create a PV:

    ```
    kubectl create -f pv-nas.yaml
    ```


**Expected result**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).
2.  In the left-side navigation pane, click **Clusters**.
3.  On the Clusters page, find the cluster that you want to manage, and click the name of the cluster or click **Details** in the **Actions** column.
4.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volumes**. Verify that the newly created PV is displayed.

## Create a PVC

When you create a PVC of the NAS type, set the `selector` parameter to configure how to select the PV to which the PVC is bound.

1.  Create a file named pvc-nas.yaml.

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


**Expected result**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).
2.  In the left-side navigation pane, click **Clusters**.
3.  On the Clusters page, find the cluster that you want to manage, and click the name of the cluster or click **Details** in the **Actions** column.
4.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volume Claims**. Verify that the newly created PVC is displayed.

## Create an application

1.  Create a file named nas.yaml.

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


**Expected result**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).
2.  In the left-side navigation pane, click **Clusters**.
3.  On the Clusters page, find the cluster that you want to manage, and click the name of the cluster or click **Applications** in the **Actions** column.
4.  In the left-side navigation pane of the cluster details page, choose **Workloads** \> **Deployments**. Verify that the newly created application is displayed.

## Verify data sharing

1.  Run the following command to query the pods that run the application.

    ```
    kubectl get pod
    ```

    Expected output:

    ```
    NAME                             READY   STATUS    RESTARTS   AGE
    nas-static-f96b6b5d7-r****       1/1     Running   0          9m
    nas-static-f96b6b5d7-w****       1/1     Running   0          9m
    ```

2.  Run the following commands to query the files in the /data path:

    ```
    kubectl exec nas-static-f96b6b5d7-r**** ls /data                    
    ```

    Expected output:

    ```
    kubectl exec nas-static-f96b6b5d7-w**** ls /data                   
    ```

    **Note:** The output indicates that no file exists in the /data path.

3.  Run the following command to create a file named nas in the /data path of a pod:

    ```
    kubectl exec nas-static-f96b6b5d7-r**** touch /data/nas
    ```

4.  Query files in the pods.

    Run the following command to query files in the /data path of one pod:

    ```
    kubectl exec nas-static-f96b6b5d7-r**** ls /data
    ```

    Expected output:

    ```
    nas
    ```

    Run the following command to query files in the /data path of the other pod:

    ```
    kubectl exec nas-static-f96b6b5d7-w**** ls /data
    ```

    Expected output:

    ```
    nas
    ```

    **Note:** The file was created in the /data path of one of the pods. However, you can find the file in the /data path of both pods. This indicates that the pods share the NAS volume.


## Verify data persistence

1.  Run the following commands to delete all pods of the application:

    ```
    kubectl delete pod nas-static-f96b6b5d7-r**** nas-static-f96b6b5d7-wthmb
    ```

    Expected output:

    ```
    pod "nas-static-f96b6b5d7-r****" deleted
    pod "nas-static-f96b6b5d7-w****" deleted
    ```

2.  Run the following command to view how the pods are deleted and recreated:

    ```
    kubectl get pod -w -l app=nginx
    ```

    Expected output:

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

3.  Run the following command to query the newly created pods:

    ```
    kubectl get pod
    ```

    Expected output:

    ```
    NAME                             READY   STATUS    RESTARTS   AGE
    nas-static-f96b6b5d7-n****       1/1     Running   0          21s
    nas-static-f96b6b5d7-w****       1/1     Running   0          21s
    ```

4.  Query files in the pods.

    Run the following command to query files in the /data path of one pod:

    ```
    kubectl exec nas-static-f96b6b5d7-n**** ls /data
    ```

    Expected output:

    ```
    nas
    ```

    Run the following command to query files in the /data path of the other pod:

    ```
    kubectl exec nas-static-f96b6b5d7-w**** ls /data
    ```

    Expected output:

    ```
    nas
    ```

    **Note:** The nas file still exists. This indicates that data is persisted to the NAS volume.


