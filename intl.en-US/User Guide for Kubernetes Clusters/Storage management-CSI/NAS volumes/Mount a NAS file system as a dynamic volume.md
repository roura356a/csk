# Mount a NAS file system as a dynamic volume

This topic describes how to use the Container Storage Interface \(CSI\) driver to mount a Network Attached Storage \(NAS\) file system as a dynamic volume to a cluster of Container Service for Kubernetes \(ACK\) in subpath and filesystem modes.

The CSI driver is installed in the ACK cluster where you want to mount a NAS file system. By default, the CSI driver is installed in ACK clusters.

If the CSI driver is not installed in the ACK cluster, you can manually install it. For more information, see [Install the plug-in](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Install the plug-in.md).

## Mount a NAS file system in subpath mode

The subpath mode is applicable to scenarios where you need to share a NAS file system among different applications or pods. You can also use this mode to mount different subdirectories of the same NAS file system to different pods.

To mount a NAS file system in subpath mode, you must manually create a NAS file system and a mount target. The following procedure demonstrates how to mount a NAS file system in subpath mode:

1.  Create a NAS file system and a mount target.

    1.  Log on to the [NAS console](https://nas.console.aliyun.com/).

    2.  Create a NAS file system. For more information, see [Manage file systems]().

    3.  Create a mount target. For more information, see [Manage mount targets]().

2.  Create a StorageClass.

    1.  Create an alicloud-nas-subpath.yaml file and paste the following content into the file:

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
        |mountOptions|Set the options parameter and Network File System \(NFS\) version in the mountOptions field.|
        |volumeAs|You can select subpath or filesystem. subpath indicates that the a subdirectory is mounted to the cluster while filesystem indicates that a file system is mounted to the cluster.|
        |server|When you mount a subdirectory of the NAS file system as a dynamic PV, this parameter specifies the mount target of the NAS file system.|
        |reclaimPolicy|The policy for reclaiming the PV.|
        |archiveOnDelete|This parameter specifies whether to delete the backend storage when reclaimPolicy is set to Delete. NAS is a shared storage service. You must set both reclaimPolicy and deleteVolume to ensure data security. The default value is true.|

    2.  Run the following command to create a StorageClass:

        ```
        kubectl create -f alicloud-nas-subpath.yaml
        ```

3.  Create a PV, a PVC, and pods to mount the NAS file system.

    In this example, two pods named `nginx-1` and `nginx-2` are created. They share a directory of the created file system. The following code blocks show the content of the `pvc.yaml`, `nginx-1.yaml`, and `nginx-2.yaml` files:

    `pvc.yaml`

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

    `nginx-1.yaml`:

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

    `nginx-2.yaml`:

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

    Run the following commands to create and query the PVC and Deployments:

    ```
    kubectl create -f pvc.yaml -f nginx-1.yaml -f nginx-2.yaml
    ```

    Run the following command to query the pods:

    ```
    kubectl get pod
    ```

    The following command output appears:

    ```
    NAME                                READY   STATUS    RESTARTS   AGE
    deployment-nas-1-5b5cdb85f6-nhklx   1/1     Running   0          32s
    deployment-nas-2-c5bb4746c-4jw5l    1/1     Running   0          32s
    ```

    **Note:** The subdirectory `xxxxxxx.cn-hangzhou.nas.aliyuncs.com:/share/nas-79438493-f3e0-11e9-bbe5-00163e09c2be` of the NAS file system is mounted to the /data directory of the pods `deployment-nas-1-5b5cdb85f6-nhklx` and `deployment-nas-2-c5bb4746c-4jw5l`. Take note of the following parameters:

    -   `/share`: the subdirectory specified in the StorageClass.
    -   `nas-79438493-f3e0-11e9-bbe5-00163e09c2be`: the name of the PV that is created to mount the NAS file system.
    To mount different subdirectories of a NAS file system to different pods, you must create a separate PVC for each pod. In this example, pvc-1 is created for nginx-1 and pvc-2 is created for nginx-2.


## Mount a NAS file system in filesystem mode

**Note:** By default, if you delete a PV that is mounted in filesystem mode, the system retains the related NAS file system and the mount target. To delete the NAS file system and the mount target together with the PV, set reclaimPolicy to Delete and deleteVolume to **true** in the StorageClass.

The filesystem mode is applicable to scenarios where you want to dynamically create and delete NAS file systems and mount targets.

When you mount a NAS volume in filesystem mode, you can create only one NAS file system and one mount target for each pod. You cannot share a NAS volume among multiple pods. The following procedure demonstrates how to mount a NAS file system as a dynamic volume in filesystem mode:

1.  Configure a Resource Access Management \(RAM\) policy and attach the policy to a RAM role.

    The filesystem mode supports dynamic creation and deletion of NAS file systems and mount targets. To enable this feature, you must grant the required permissions to csi-nasprovisioner. The following code block shows a permission policy that contains the required permissions:

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

    You can grant the permissions by using one of the following methods:

    -   Attach the preceding permission policy to the RAM role of the master nodes in your ACK cluster For more information, see [ACK default roles](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/ACK default roles.md).

        ![Attach a custom policy to the RAM role of mater nodes](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7474031161/p69183.png)

        **Note:** The RAM role of master nodes is automatically assigned to a managed Kubernetes cluster. However, you must manually assign the RAM role of master nodes to a dedicated Kubernetes cluster.

    -   Create a RAM user and attach the preceding permission policy to the RAM user. Then, generate an AccessKey pair for the RAM user and specify the AccessKey pair in the env variable of csi-nasprovisioner. For more information, see [ACK default roles](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/ACK default roles.md).

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

    1.  Create an alicloud-nas-fs.yaml file and paste the following content into the file:

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
          vpcId: "vpc-xxxxxxxxxxxx"
          vSwitchId: "vsw-xxxxxxxxx"
          deleteVolume: "false"
        provisioner: nasplugin.csi.alibabacloud.com
        reclaimPolicy: Retain
        ```

        |Parameter|Description|
        |---------|-----------|
        |volumeAs|The mode in which the NAS file system is mounted. Supported modes are:         -   filesystem: csi-nasprovisioner automatically creates a NAS file system. Each PV mounts a separate NAS file system.
        -   subpath: csi-nasprovisioner automatically creates a subdirectory in a NAS file system. Each PV mounts a separate subdirectory of a NAS file system. |
        |storageType|The type of NAS file system. You can select Performance or Capacity. The default is Performance.|
        |zoneId|The ID of the zone to which the NAS file system belongs.|
        |vpcId|The ID of the virtual private cloud \(VPC\) to which the mount target of the NAS file system belongs.|
        |vSwitchId|The ID of the vSwitch to which the mount target of the NAS file system belongs.|
        |accessGroupName|The permission group to which the mount target of the NAS file system belongs. The default is DEFAULT\_VPC\_GROUP\_NAME.|
        |deleteVolume|Specifies whether to automatically delete the related NAS file system when a PV is deleted. NAS is a shared storage service. Therefore, you must specify both deleteVolume and reclaimPolicy to ensure data security.|
        |reclaimPolicy|Specifies the reclaim policy. When you delete a PV, the related NAS file system is automatically deleted only if you set deleteVolume to true and reclaimPolicy to Delete.|

    2.  Run the following command to create a StorageClass:

        ```
        kubectl create -f alicloud-nas-fs.yaml
        ```

3.  Create a PV, a PVC, and pods to mount the NAS file system.

    The following code blocks shows the content of the `pvc.yaml` and `nginx.yaml` files:

    `pvc.yaml`

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

    `nginx.yaml`

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

    Run the following command to create the PVC and Deployments:

    ```
    kubectl create -f pvc.yaml -f nginx.yaml
    ```


In filesystem mode, the CSI driver automatically creates a NAS file system and a mount target when you create the PVC. If you set deleteVolume to true and reclaimPolicy to Delete, the file system and the mount target are automatically deleted when the PVC is deleted.

