---
keyword: [NAS, storage sharing, persistent storage]
---

# Use a NAS file system to share and persist data

You can use an Apsara File Storage NAS \(NAS\) file system to persist data and share the data across multiple pods. This topic describes how to use a NAS file system to persist and share data.

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   A file system is created in the NAS File System console. For more information, see [Mount an NFS file system](). The file system that you create and the Kubernetes cluster must be deployed in the same zone.
-   A mount target of the Kubernetes cluster is added to the file system. For more information, see [Mount an SMB file system on Windows](). Make sure that the NAS file system and the Kubernetes cluster are connected to the same virtual private cloud \(VPC\).

If a NAS file system is mounted to multiple pods, data in the file system is shared by the pods. In this case, the application must be capable of automatically synchronizing application data across pods.

**Note:** You are not allowed to grant permissions to access the /directory \(root directory\) of the NAS file system. The user account and user group to which the directory belongs cannot be modified.

## When to use a NAS file system:

-   You want to run applications that require high disk I/O throughput.
-   You need a storage service that offers higher read and write throughput than Object Storage Service \(OSS\).
-   You want to share files across hosts. For example, you want to use a NAS file system as a file server.

## Mount a NAS file system as a statically provisioned volume

**Step 1: Create a PV**

1.  Use the following template to create a pv-nas.yaml file:

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
      csi:
        driver: nasplugin.csi.alibabacloud.com
        volumeHandle: pv-nas
        volumeAttributes:
          server: "2564f49129-ysu87.cn-shenzhen.nas.aliyuncs.com"
          path: "/csi"
      mountOptions:
      - nolock,tcp,noresvport
      - vers=3
    ```

    |Parameter|Description|
    |---------|-----------|
    |`name`|The name of the PV.|
    |`labels`|The labels that are added to the PV.|
    |`storage`|The available storage of the NAS file system.|
    |`accessModes`|The access mode of the PV.|
    |`driver`|The type of driver. In this example, the parameter is set to `nasplugin.csi.alibabacloud.com`. This indicates that the Alibaba Cloud NAS CSI plug-in is used.|
    |`volumeHandle`|The name of the PV.|
    |`server`|The mount target of the NAS file system.|
    |`path`|The subdirectory of the NAS file system that is mounted. If you mount an Extreme NAS file system, the path must start with /share.|
    |`vers`|The version of the NFS protocol. We recommend that you use NFSv3. Extreme NAS file systems support only NFSv3.|

2.  Run the following command to create a PV:

    ```
    kubectl create -f pv-nas.yaml
    ```


**Step 2: Create a PVC**

Create a PVC for the NAS file system. Set the parameters in the selector field to specify the PV with which the PVC is associated.

1.  Use the following template to create a pvc-nas.yaml file:

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

    |Parameter|Description|
    |---------|-----------|
    |name|The name of the PVC.|
    |accessModes|The access mode of the PVC.|
    |storage|The storage that is required by the application. The storage cannot exceed that of the NAS file system.|
    |mathLabels|The labels are used to associate the PVC with the PV. The labels must be same as those of the PV.|

2.  Run the following command to create a PVC:

    ```
    kubectl create -f pvc-nas.yaml
    ```


**Step 3: Deploy an application**

1.  Use the following template to create a nas.yaml file:

    Deploy an application named nas-static and associate the PVC with the application.

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

    -   `mountPath`: the path where the NAS file system is mounted in the container.
    -   `claimName`: the name of the PVC, which is used to associate the application with the PVC.
2.  Run the following command to deploy the application:

    ```
    kubectl create -f nas.yaml
    ```


## Mount a NAS file system as a dynamically provisioned volume

**Step 1: Create a StorageClass**

1.  Use the following template to create a alicloud-nas-subpath.yaml file.

    ```
    apiVersion: storage.k8s.io/v1
    kind: StorageClass
    metadata:
      name: alicloud-nas-subpath
    mountOptions:
    - nolock,tcp,noresvport
    - vers=3
    parameters:
      volumeAs: subpath
      server: "xxxxxxx.cn-hangzhou.nas.aliyuncs.com:/k8s/"
    provisioner: nasplugin.csi.alibabacloud.com
    reclaimPolicy: Retain
    ```

    |Parameter|Description|
    |---------|-----------|
    |`name`|The name of the StorageClass.|
    |`provisioner`|The value is set to alicloud/disk to automatically create a disk by using the provisioner plug-in.|
    |`reclaimPolicy`|Policy for reclaiming the NAS file system. Valid values: Delete and Retain. Default value: Delete. **Note:** If you set the value to Delete, the NAS file system is automatically deleted when you delete the PVC. The data in the NAS file system cannot be recovered. |

2.  Run the following command to create a StorageClass:

    ```
    kubectl create -f alicloud-nas-subpath.yaml
    ```


**Step 2: Create a PVC**

1.  Use the following template to create a pvc.yaml file:

    ```
    kind: PersistentVolumeClaim
    apiVersion: v1
    metadata:
      name: nas-csi-pvc
    spec:
      accessModes:
        - ReadWriteMany
      storageClassName: alicloud-nas-subpath
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
    kubectl create -f pvc. yaml
    ```


**Step 3: Deploy applications**

Deploy two applications named nginx-1 and nginx-2 to share the same subdirectory of the NAS file system.

**Note:** To mount different subdirectories of a NAS file system to different pods, you must create a separate PVC for each pod. In this example, pvc-1 is created for nginx-1 and pvc-2 is created for nginx-2.

1.  Use the following content to create a nginx-1.yam file.

    Deploy an application named nginx-1 and associate the PVC with the application.

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: deployment-nas-1
      labels:
        app: nginx-1
    spec:
      selector:
        matchLabels:
          app: nginx-1
      template:
        metadata:
          labels:
            app: nginx-1
        spec:
          containers:
          - name: nginx
            image: nginx:1.7.9
            ports:
            - containerPort: 80
            volumeMounts:
              - name: nas-pvc
                mountPath: "/data"
          volumes:
            - name: nas-pvc
              persistentVolumeClaim:
                claimName: nas-csi-pvc
    ```

    -   `mountPath`: the path where the NAS file system is mounted in the container.
    -   `claimName`: the name of the PVC, which is used to associate the application with the PVC. In this case, the value is set to nas-csi-pvc.
2.  Use the following content to create a nginx-2.yaml file.

    Deploy an application named nginx-2 and associate the PVC with the application.

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: deployment-nas-2
      labels:
        app: nginx-2
    spec:
      selector:
        matchLabels:
          app: nginx-2
      template:
        metadata:
          labels:
            app: nginx-2
        spec:
          containers:
          - name: nginx
            image: nginx:1.7.9
            ports:
            - containerPort: 80
            volumeMounts:
              - name: nas-pvc
                mountPath: "/data"
          volumes:
            - name: nas-pvc
              persistentVolumeClaim:
                claimName: nas-csi-pvc
    ```

    -   `mountPath`: the path where the NAS file system is mounted in the container. In this example, the value is set to /data.
    -   `claimName`: Enter the same PVC name as the nginx-1 application. In this example, the value is set to nas-csi-pvc.
3.  Run the following command to deploy the nginx-1 and nginx-2 applications:

    ```
    kubectl create -f nginx-1.yaml -f nginx-2.yaml
    ```

4.  Run the following command to view the pods that are provisioned for the two applications:

    ```
    kubectl get pod
    ```

    Expected output:

    ```
    NAME                                READY   STATUS    RESTARTS   AGE
    deployment-nas-1-5b5cdb85f6-n****   1/1     Running   0          32s
    deployment-nas-2-c5bb4746c-4****    1/1     Running   0          32s
    ```

    **Note:** The subdirectory `/share/nas-79438493-f3e0-11e9-bbe5-00163e09c2be` of the NAS file system is mounted to the /data directory in pods `deployment-nas-1-5b5cdb85f6-nhklx` and `deployment-nas-2-c5bb4746c-4jw5l`. Take note of the following parameters:

    -   `/share`: the subdirectory specified in the StorageClass.
    -   `nas-79438493-f3e0-11e9-bbe5-00163e09c2be`: the name of the PV that is created to mount the NAS file system.

## Verify that the NAS file system can be used to persist data

Both dynamically provisioned and statically provisioned NAS volumes support persistent storage. In this example, the dynamically provisioned NAS volume is used.

1.  Query the pods that host the applications and the NAS file system.

    1.  Run the following command to query the pods that host the applications:

        ```
        kubectl get pod 
        ```

        Expected output:

        ```
        NAME                                READY   STATUS    RESTARTS   AGE
        deployment-nas-1-5b5cdb85f6-n****   1/1     Running   0          32s
        deployment-nas-2-c5bb4746c-4****    1/1     Running   0          32s
        ```

    2.  Run the following command to query the file in the /data path of a pod. The pod deployment-nas-1-5b5cdb85f6-n\*\*\*\* is used as an example:

        ```
        kubectl exec deployment-nas-1-5b5cdb85f6-n**** ls /data
        ```

        No output is returned. This indicates that no file exists in the /data path.

2.  Run the following command to create a file named nas in the /data path of the pod deployment-nas-1-5b5cdb85f6-n\*\*\*\*:

    ```
    kubectl exec deployment-nas-1-5b5cdb85f6-n**** touch /data/nas
    ```

3.  Run the following command to query the file in the /data path of the pod deployment-nas-1-5b5cdb85f6-n\*\*\*\*:

    ```
    kubectl exec deployment-nas-1-5b5cdb85f6-n**** ls /data
    ```

    Expected output:

    ```
    nas
    ```

4.  Run the following command to delete the pod:

    ```
    kubectl delete pod deployment-nas-1-5b5cdb85f6-n****
    ```

5.  Open another kubectl command-line interface \(CLI\) and run the following command to query how the pod is deleted and recreated:

    ```
    kubectl get pod -w -l app=nginx
    ```

6.  Verify that the file still exists in the /data path after the pod is deleted.

    1.  Run the following command to query the name of the recreated pod:

        ```
        kubectl get pod
        ```

        Expected output:

        ```
        NAME                                READY   STATUS    RESTARTS   AGE
        deployment-nas-1-5b5cdb85f6-n****   1/1     Running   0          32s
        deployment-nas-2-c5bb4746c-4****    1/1     Running   0          32s
        ```

    2.  Run the following command to query the file in the /data path of the pod deployment-nas-1-5b5cdb85f6-n\*\*\*\*:

        ```
        kubectl exec deployment-nas-1-5b5cdb85f6-n**** ls /data
        ```

        Expected output:

        ```
        nas
        ```

        The nas file still exists in the /data path. This indicates that data is persisted to the NAS file system.


## Verify that the data in the NAS file system can be shared across multiple pods

Both dynamically provisioned and statically provisioned NAS volumes support data sharing. In this example, the dynamically provisioned NAS volume is used.

1.  Query the pods that host the applications and the files in the NAS file system.

    1.  Run the following command to query the pods that host the applications:

        ```
        kubectl get pod 
        ```

        Expected output:

        ```
        NAME                                READY   STATUS    RESTARTS   AGE
        deployment-nas-1-5b5cdb85f6-n****   1/1     Running   0          32s
        deployment-nas-2-c5bb4746c-4****    1/1     Running   0          32s
        ```

    2.  Run the following command to query files in the /data path of each pod:

        ```
        kubectl exec deployment-nas-1-5b5cdb85f6-n**** ls /data
        kubectl exec deployment-nas-2-c5bb4746c-4**** ls /data
        ```

2.  Run the following command to create the nas file in the /data path of a pod:

    ```
     kubectl exec deployment-nas-1-5b5cdb85f6-n**** touch /data/nas
    ```

3.  Run the following command to view files in the /data path of each pod:

    1.  Run the following command to view the file in the /data path of the pod deployment-nas-1-5b5cdb85f6-n\*\*\*\*:

        ```
        kubectl exec deployment-nas-1-5b5cdb85f6-n**** ls /data
        ```

        Expected output:

        ```
        nas
        ```

    2.  Run the following command to view the file in the /data path of the pod deployment-nas-2-c5bb4746c-4\*\*\*\*:

        ```
        kubectl exec deployment-nas-2-c5bb4746c-4**** ls /data
        ```

        Expected output:

        ```
        nas
        ```

        When you create a file in the /data path of one pod, you can also find the file in the /data path of the other pod. This indicates that the NAS file system is shared by the two pods.


