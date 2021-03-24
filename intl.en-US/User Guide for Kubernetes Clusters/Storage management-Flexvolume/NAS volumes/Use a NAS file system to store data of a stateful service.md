# Use a NAS file system to store data of a stateful service

This topic describes the scenario where a NAS file system is used to store data of a stateful service and multiple pods share data.

-   A Kubernetes cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   The Kubernetes cluster is connected by using kubectl. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).
-   A file system is created in the NAS File System console. For more information, see [Mount an NFS file system](). The NAS file system and the Kubernetes cluster reside in the same zone.
-   The mount point for the Kubernetes cluster is added to the file system. For more information, see [Mount an SMB file system on Windows](). Make sure that the file system and the Kubernetes cluster are connected to the same VPC.

If a NAS file system is mounted to multiple pods, data in the file system is shared by the pods. If application data is modified in one pod, the modification must be synchronized to other pods.

Scenarios

-   You want to run applications that require high disk I/O performance.
-   You need a storage service that provides higher read and write performance than Object Storage Service \(OSS\).
-   You want to share files cross hosts. For example, you want to use a NAS file system as a file server.

Procedure

1.  Create a NAS file system and add a mount point to it.
2.  Create a persistent volume \(PV\) and a persistent volume claim \(PVC\).

This following content describes how create a PV or PVC by using the flexvolume plug-in provided by Alibaba Cloud to use Alibaba Cloud File Storage NAS.

## Create a PV

1.  Create the pv-nas.yaml file.

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
          server: "***-**.cn-hangzhou.nas.aliyuncs.com"   ////Replace the value with the actual mount point.
          path: "/k8s1"
          vers: "4.0"
    ```

    |Parameter|Description|
    |---------|-----------|
    |`alicloud-pvname`|The name of the PV.|
    |`server`|The mount point of the NAS file system. To view the mount point, log on to the NAS File System console, click **File System List** in the left-side navigation pane, find the file system that you want to mount, and click **Management** in the **Operations** column. The mount point appears in the **Mount Address** column in the Mount Point section.|
    |`path`|The mount directory of the NAS file system. You can mount a subdirectory of a NAS file system. If no subdirectories exist, the system automatically creates a subdirectory and mounts it to the Kubernetes cluster.|
    |`vers`|\(Optional\) The version number of the NFS mount protocol. Valid values: 3 and 4.0. Default value: 3.|
    |`mode`|\(Optional\) Specifies the permission to access the mount directory. By default, this parameter is empty.**Note:**

    -   You cannot grant the permission to access the root directory to which the NAS file system is mounted.
    -   If you set the `mode` parameter for a NAS file system that stores a large amount of data, the process of mounting the NAS file system may take a long time or even fail. We recommend that you leave this parameter empty. |

2.  Run the following command to create a PV:

    ```
    kubectl create -f pv-nas.yaml
    ```


Verify the result:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).
2.  In the left-side navigation pane, click **Clusters**.
3.  On the Clusters page, find the cluster to which you mounted the NAS file system, and click the cluster name or **Details** in the **Actions** column.
4.  In the left-side pane, click **Persistent Volumes**.
5.  Click the **Persistent Volumes** tab to view the PV that you created.

## Create a PVC

Before you create a PVC for a NAS file system, you must set the `selector` parameter to specify the PV with which the PVC is associated.

1.  Create the pvc-nas.yaml file.

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


Verify the result:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).
2.  In the left-side navigation pane, click **Clusters**.
3.  On the Clusters page, find the cluster to which you mounted the NAS file system, and click the cluster name or **Details** in the **Actions** column.
4.  In the left-side pane, click **Persistent Volumes**.
5.  Click the **Persistent Volume Claims** tab to view the PVC that you created.

## Create a deployment

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

2.  Run the following command to create an application:

    ```
    kubectl create -f nas.yaml
    ```


Verify the result:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).
2.  In the left-side navigation pane, click **Clusters**.
3.  On the Clusters page, find the cluster to which you mounted the NAS file system, and click the cluster name or **Applications** in the **Actions** column.
4.  In the left-side navigation pane on the page that appears, click **Workload**.
5.  On the page that appears, click the **Deployments**tab to view the deployment that you just created.

## Verify that the NAS file system is shared by pods

1.  Run the following command to view the pods in which the deployment resides:

    ```
    kubectl get pod
    ```

    ```
    NAME                             READY   STATUS    RESTARTS   AGE
    nas-static-f96b6b5d7-r****       1/1     Running   0          9m
    nas-static-f96b6b5d7-w****       1/1     Running   0          9m
    ```

2.  Run the following commands to view the files in the /data path:

    ```
    kubectl exec nas-static-f96b6b5d7-r**** ls /data                    
    ```

    ```
    kubectl exec nas-static-f96b6b5d7-w**** ls /data                   
    ```

    **Note:** The two /data paths are empty.

3.  Run the following command to create file nas in the /data path of one pod:

    ```
    kubectl exec nas-static-f96b6b5d7-r**** touch /data/nas
    ```

4.  Run the following command to view the file in the /data path of each node:

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

    **Note:** The file exists in the /data path of both pods. This indicates that the two pods share the NAS file system.


## Verify that data in the NAS file system is stored persistently

1.  Run the following commands to delete all pods of the application:

    ```
    kubectl delete pod nas-static-f96b6b5d7-r**** nas-static-f96b6b5d7-wthmb
    ```

    ```
    pod "nas-static-f96b6b5d7-r****" deleted
    pod "nas-static-f96b6b5d7-w****" deleted
    ```

2.  Run the following command to view the process in which the original pods are removed and new pods are created by Kubernetes:

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

3.  Run the following command to view the new pods:

    ```
    kubectl get pod
    ```

    ```
    NAME                             READY   STATUS    RESTARTS   AGE
    nas-static-f96b6b5d7-n****       1/1     Running   0          21s
    nas-static-f96b6b5d7-w****       1/1     Running   0          21s
    ```

4.  Run the following command to view files in the /data path of each pod:

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

    **Note:** The nas file still exists. This indicates that data in the NAS file system can be permanently stored.


