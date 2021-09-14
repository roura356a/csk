---
keyword: [NAS, dynamically provisioned volume, persistent storage, shared storage]
---

# Use a dynamically provisioned NAS volume

You can use the Container Storage Interface \(CSI\) driver to mount a dynamically provisioned Apsara File Storage NAS \(NAS\) volume to a Container Service for Kubernetes \(ACK\) cluster in subpath and filesystem modes. This topic describes how to use a dynamically provisioned NAS volume and how to enable persistent storage and shared storage by using dynamically provisioned NAS volumes.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   A NAS file system is created. For more information, see [Create a NAS file system]().

    If you want to encrypt data in a NAS volume, configure the encryption settings when you create the NAS file system.

-   A mount target is created for the NAS file system. For more information, see [Manage mount targets]().

    The mount target and the cluster node to which you want to mount the NAS file system must belong to the same virtual private cloud \(VPC\).


## Scenarios

-   Your application requires high disk I/O.
-   You need a storage service that offers higher read and write throughput than Object Storage Service \(OSS\).
-   You want to share files across hosts. For example, you want to use a NAS file system as a file server.

## Usage notes

-   To mount an Extreme NAS file system, set the `path` parameter of the NAS volume to a subdirectory of /share. For example, a value of `0cd8b4a576-g****.cn-hangzhou.nas.aliyuncs.com:/share/subpath` indicates that the mounted subdirectory of the NAS file system is `/share/subpath`.
-   If a NAS file system is mounted to multiple pods, the data in the file system is shared by the pods. In this case, the application must be able to synchronize data across these pods when data modifications are made by multiple pods.

    **Note:** You cannot grant permissions to access the / directory \(root directory\) of the NAS file system. The user account and user group to which the directory belongs cannot be modified.

-   If the securityContext.fsgroup parameter is set in the application template, kubelet performs the `chmod` or `chown` operation after the volume is mounted, which increases the time consumption.

## Mount a dynamically provisioned NAS volume in subpath mode

The subpath mode is applicable to scenarios where you want to share a NAS volume among different applications or pods. You can also use this mode to mount different subdirectories of the same NAS file system to different pods.

To mount a dynamically provisioned NAS volume in subpath mode, you must manually create a NAS file system and a mount target.

1.  Create a NAS file system and a mount target.

    1.  Log on to the [NAS console](https://nas.console.aliyun.com/).

    2.  Create a NAS file system. For more information, see [Create a NAS file system]().

    3.  Create a mount target. For more information, see [Manage mount targets]().

2.  Create a StorageClass.

    1.  Create an alicloud-nas-subpath.yaml file and copy the following content into the file:

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
          server: "0cd8b4a576-g****.cn-hangzhou.nas.aliyuncs.com:/k8s/"
        provisioner: nasplugin.csi.alibabacloud.com
        reclaimPolicy: Retain
        ```

        |Parameter|Description|
        |---------|-----------|
        |mountOptions|Set the options parameter and specify the Network File System \(NFS\) version in the mountOptions field.|
        |volumeAs|You can select subpath or filesystem. subpath indicates that a subdirectory is mounted to the cluster. filesystem indicates that a file system is mounted to the cluster.|
        |server|When you mount a subdirectory of the NAS file system as a persistent volume \(PV\), this parameter specifies the mount target of the NAS file system.|
        |reclaimPolicy|The reclaim policy of the PV. By default, this parameter is set to Delete. You can also set this parameter to Retain.         -   Delete mode: When a persistent volume claim \(PVC\) is deleted, the related PV and NAS file system are also deleted.
        -   Retain mode: When a PVC is deleted, the related PV and NAS file system are retained and can only be manually deleted.
If you require higher data security, we recommend that you use the Retain mode to prevent data loss caused by user errors.|
        |archiveOnDelete|This parameter specifies the reclaim policy of backend storage when reclaimPolicy is set to Delete. NAS is a shared storage service. You must set both reclaimPolicy and archiveOnDelete to ensure data security. Configure the policy in the parameters section. The default value is true. This value indicates that the subdirectory or files are not deleted when the PV is deleted. Instead, the subdirectory or files are renamed in the format of `archived-{pvName}.{timestamp}`. If the value is set to false, it indicates that the backend storage is deleted when the PV is deleted. **Note:** We recommend that you do not set the value to false when the service receives a large amount of network traffic. For more information, see [What do I do if the task queue of alicloud-nas-controller is full and PVs cannot be created when I use a dynamically provisioned NAS volume?](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/NAS volumes/FAQ about NAS volumes.md). |

    2.  Run the following command to create a StorageClass:

        ```
        kubectl create -f alicloud-nas-subpath.yaml
        ```

3.  Create a PVC.

    1.  Create a pvc.yaml file and copy the following content into the file:

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
        |`storageClassName`|The name of the StorageClass that you want to associate with the PVC.|
        |`storage`|The storage that is requested by the application.|

    2.  Run the following command to create a PVC:

        ```
        kubectl create -f pvc.yaml
        ```

4.  Create applications.

    Deploy two applications named **nginx-1** and **nginx-2** to share the same subdirectory of the NAS file system.

    1.  Create an nginx-1.yml file and copy the following content into the file:

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

        -   `mountPath`: the mount path in the container where the NAS volume is mounted.
        -   `claimName`: the name of the PVC that is mounted to the application. In this example, the value is set to **nas-csi-pvc**.
    2.  Create an nginx-2.yml file and copy the following content into the file:

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

        -   `mountPath`: the mount path in the container where the NAS volume is mounted. In this example, the value is set to /data.
        -   `claimName`: Enter the name of the PVC that is mounted to **nginx-1**. In this example, the value is set to **nas-csi-pvc**.
    3.  Run the following command to deploy applications **nginx-1** and **nginx-2**:

        ```
        kubectl create -f nginx-1.yaml -f nginx-2.yaml
        ```

5.  Run the following command to query pods:

    ```
    kubectl get pod
    ```

    Expected output:

    ```
    NAME                                READY   STATUS    RESTARTS   AGE
    deployment-nas-1-5b5cdb85f6-n****   1/1     Running   0          32s
    deployment-nas-2-c5bb4746c-4****    1/1     Running   0          32s
    ```

    **Note:** The subdirectory `0cd8b4a576-g****.cn-hangzhou.nas.aliyuncs.com:/share/nas-79438493-f3e0-11e9-bbe5-00163e09****` of the NAS volume is mounted to the /data directory of pods `deployment-nas-1-5b5cdb85f6-n****` and `deployment-nas-2-c5bb4746c-4****`. The following information is displayed.

    -   `/share`: the subdirectory is mounted in subpath mode as specified in the StorageClass configurations.
    -   `nas-79438493-f3e0-11e9-bbe5-00163e09****`: the name of the PV.
    To mount different subdirectories of a NAS file system to different pods, you must create a separate PVC for each pod. To do this, you can create **pvc-1** for **nginx-1** and **pvc-2** for **nginx-2**.


## Mount a dynamically provisioned NAS volume in filesystem mode

**Note:** By default, if you delete a PV that is mounted in filesystem mode, the system retains the related NAS file system and mount target. To delete the NAS file system and mount target together with the PV, set reclaimPolicy to Delete and set deleteVolume to true in the StorageClass configurations.

The filesystem mode is applicable to scenarios where you want to dynamically create and delete NAS file systems and mount targets.

When you mount a NAS volume in filesystem mode, you can create only one NAS file system and one mount target for each pod. You cannot share a NAS volume among multiple pods. The following procedure demonstrates how to mount a dynamically provisioned NAS volume in filesystem mode.

1.  Configure a Resource Access Management \(RAM\) permission policy and attach it to a RAM role.

    The filesystem mode allows you to dynamically create and delete NAS file systems and mount targets. To enable this feature, you must grant the required permissions to csi-nasprovisioner. The following code block shows a permission policy that contains the required permissions:

    ```
    {
        "Action": [
            "nas:DescribeMountTargets",
            "nas:CreateMountTarget",
            "nas:DeleteFileSystem",
            "nas:DeleteMountTarget",
            "nas:CreateFileSystem"
        ],
        "Resource": [
            "*"
        ],
            "Effect": "Allow"
    }
    ```

    You can grant the permissions by using the following methods:

    -   Attach the preceding permission policy to the master RAM role of your ACK cluster. For more information, see [ACK default roles](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/ACK default roles.md).

        ![Attach a custom permission policy](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/2256388261/p69183.png)

        **Note:** The master RAM role is automatically assigned to a managed Kubernetes cluster. However, for a dedicated Kubernetes cluster, you must manually assign the master RAM role.

    -   Create a RAM user and attach the preceding permission policy to the RAM user. Then, generate an AccessKey pair and specify the AccessKey pair in the `env` variable in the configurations of the csi-provisioner StatefulSet. For more information, see [ACK default roles](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/ACK default roles.md).

        ```
        env:
            - name: CSI_ENDPOINT
                value: unix://socketDir/csi.sock
            - name: ACCESS_KEY_ID
                value: ""
            - name: ACCESS_KEY_SECRET
                value: ""
        ```

2.  Create a StorageClass.

    1.  Create an alicloud-nas-fs.yaml file and copy the following content into the file:

        ```
        apiVersion: storage.k8s.io/v1
        kind: StorageClass
        metadata:
          name: alicloud-nas-fs
        mountOptions:
        - nolock,tcp,noresvport
        - vers=3
        parameters:
          volumeAs: filesystem
          zoneId: cn-hangzhou-a
          vpcId: "vpc-2ze9c51qb5kp1nfqu****"
          vSwitchId: "vsw-gw8tk6gecif0eu9ky****"
          deleteVolume: "false"
        provisioner: nasplugin.csi.alibabacloud.com
        reclaimPolicy: Retain
        ```

        |Parameter|Description|
        |---------|-----------|
        |volumeAs|The mode in which the NAS file system is mounted. Supported modes are:         -   filesystem: csi-nasprovisioner automatically creates a NAS file system. Each PV corresponds to a NAS file system.
        -   subpath: csi-nasprovisioner automatically creates a subdirectory in a NAS file system. Each PV corresponds to a subdirectory of the NAS file system. |
        |storageType|The type of NAS file system. You can select **Performance** or **Capacity**. Default value: Performance.|
        |zoneId|The ID of the zone to which the NAS file system belongs.|
        |vpcId|The ID of the VPC to which the mount target of the NAS file system belongs.|
        |vSwitchId|The ID of the vSwitch to which the mount target of the NAS file system belongs.|
        |accessGroupName|The permission group to which the mount target of the NAS file system belongs. Default value: DEFAULT\_VPC\_GROUP\_NAME.|
        |deleteVolume|The reclaim policy of the NAS file system when the related PV is deleted. NAS is a shared storage service. Therefore, you must specify both deleteVolume and reclaimPolicy to ensure data security.|
        |reclaimPolicy|The reclaim policy of the PV. When you delete a PVC, the related NAS file system is automatically deleted only if you set deleteVolume to true and reclaimPolicy to Delete.|

    2.  Run the following command to create a StorageClass:

        ```
        kubectl create -f alicloud-nas-fs.yaml
        ```

3.  Create a PVC and pods to mount a NAS volume.

    1.  Create a pvc.yaml file and copy the following content into the file:

        ```
        kind: PersistentVolumeClaim
        apiVersion: v1
        metadata:
          name: nas-csi-pvc-fs
        spec:
          accessModes:
            - ReadWriteMany
          storageClassName: alicloud-nas-fs
          resources:
            requests:
              storage: 20Gi
        ```

    2.  Create an nginx.yaml file and copy the following content into the file:

        ```
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: deployment-nas-fs
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
                image: nginx:1.7.9
                ports:
                - containerPort: 80
                volumeMounts:
                  - name: nas-pvc
                    mountPath: "/data"
              volumes:
                - name: nas-pvc
                  persistentVolumeClaim:
                    claimName: nas-csi-pvc-fs
        ```

    3.  Run the following command to create the PV and PVC:

        ```
        kubectl create -f pvc.yaml -f nginx.yaml
        ```


In filesystem mode, the CSI driver automatically creates a NAS file system and a mount target when you create the PVC. When the PVC is deleted, the file system and the mount target are retained or deleted based on the settings of the deleteVolume and reclaimPolicy parameters.

## Verify that the NAS file system can be used to persist data

1.  Query the pods that run the application and the files in the mounted NAS file system.

    1.  Run the following command to query the pods that run the application:

        ```
        kubectl get pod 
        ```

        Expected output:

        ```
        NAME                                READY   STATUS    RESTARTS   AGE
        deployment-nas-1-5b5cdb85f6-n****   1/1     Running   0          32s
        deployment-nas-2-c5bb4746c-4****    1/1     Running   0          32s
        ```

    2.  Run the following command to query files in the /data path of a pod. The pod named `deployment-nas-1-5b5cdb85f6-n****` is used as an example:

        ```
        kubectl exec deployment-nas-1-5b5cdb85f6-n**** -- ls /data
        ```

        No output is returned. This indicates that no file exists in the /data path.

2.  Run the following command to create a file named nas in the /data path of the pod `deployment-nas-1-5b5cdb85f6-n****`:

    ```
    kubectl exec deployment-nas-1-5b5cdb85f6-n**** -- touch /data/nas
    ```

3.  Run the following command to query files in the /data path of the pod `deployment-nas-1-5b5cdb85f6-n****`:

    ```
    kubectl exec deployment-nas-1-5b5cdb85f6-n**** -- ls /data
    ```

    Expected output:

    ```
    nas
    ```

4.  Run the following command to delete the pod:

    ```
    kubectl delete pod deployment-nas-1-5b5cdb85f6-n****
    ```

5.  Open another command-line interface \(CLI\) and run the following command to view how the pod is deleted and recreated:

    ```
    kubectl get pod -w -l app=nginx
    ```

6.  Verify that the file still exists after the pod is deleted.

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

    2.  Run the following command to query files in the /data path of the pod `deployment-nas-1-5b5cdb85f6-n****`:

        ```
        kubectl exec deployment-nas-1-5b5cdb85f6-n**** -- ls /data
        ```

        Expected output:

        ```
        nas
        ```

        The nas file still exists in the /data path. This indicates that data is persisted to the NAS file system.


## Verify that data in the NAS file system can be shared across pods

1.  Query the pods that run the application and the files in the mounted NAS file system.

    1.  Run the following command to query the pods that run the application:

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
        kubectl exec deployment-nas-1-5b5cdb85f6-n**** -- ls /data
        kubectl exec deployment-nas-2-c5bb4746c-4**** -- ls /data
        ```

2.  Run the following command to create a file named nas in the /data path of a pod:

    ```
     kubectl exec deployment-nas-1-5b5cdb85f6-n**** -- touch /data/nas
    ```

3.  Query files in the /data path of each pod.

    1.  Run the following command to query files in the /data path of the pod `deployment-nas-1-5b5cdb85f6-n****`:

        ```
        kubectl exec deployment-nas-1-5b5cdb85f6-n**** -- ls /data
        ```

        Expected output:

        ```
        nas
        ```

    2.  Run the following command to query files in the /data path of the pod `deployment-nas-2-c5bb4746c-4****`:

        ```
        kubectl exec deployment-nas-2-c5bb4746c-4**** -- ls /data
        ```

        Expected output:

        ```
        nas
        ```

        When you create a file in the /data path of one pod, you can also find the file in the /data path of the other pod. This indicates that data in the NAS file system is shared by the two pods.


