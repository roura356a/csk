# Mount a dynamic NAS volume

Container Service for Kubernetes \(ACK\) allows you to use the Container Storage Interface \(CSI\) driver to mount a dynamic NAS volume to a cluster in the subpath and filesystem modes.

The CSI driver is installed for the target cluster. By default, the CSI driver is installed for Kubernetes clusters.

If a cluster does not have the CSI driver installed, you can manually install it. For more information, see [Install the plug-in](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Install the plug-in.md).

## Mount a dynamic NAS volume in the subpath mode

We recommend that you use the subpath mode when you mount one NAS volume to multiple pods for data sharing or mount different volumes in the same NAS file system to different pods.

Before you mount a dynamic NAS volume in the subpath mode, you must manually create a NAS file system and a mount point.

1.  Create a NAS file system and a mount point.

    1.  Log on to the [NAS console](https://nas.console.aliyun.com/).

    2.  Create a NAS file system. For more information, see [Manage file systems]().

        ![Create a NAS file system](../images/p69131.png)

    3.  Create a mount point. For more information, see [Manage mount targets]().

        ![Create a mount point](../images/p69132.png)

2.  Create a StorageClass.

    1.  Write the following content to the alicloud-nas-subpath.yaml file.

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

    2.  Run the following command to create a StorageClass:

        ```
        kubectl create -f alicloud-nas-subpath.yaml
        ```

3.  Create a persistent volume claim \(PVC\) and pods nginx-1 and nginx-2, and mount a NAS volume to the pods.

    Write the following content to the `pvc.yaml`, `nginx-1.yaml`, and `nginx-2.yaml` files.````

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

    Run the following commands to deploy the preceding YAML files and query pods:

    ```
    kubectl create -f pvc.yaml -f nginx-1.yaml -f nginx-2.yaml
    
    kubectl get po
    NAME                                READY   STATUS    RESTARTS   AGE
    deployment-nas-1-5b5cdb85f6-nhklx   1/1     Running   0          32s
    deployment-nas-2-c5bb4746c-4jw5l    1/1     Running   0          32s
    ```

    **Note:** The subdirectory `/share/nas-79438493-f3e0-11e9-bbe5-00163e09c2be` in the NAS file system is mounted to the /data subdirectory of pods `nginx-1` and `nginx-2`.

    -   `/share`: the subdirectory specified in the StorageClass.
    -   `nas-79438493-f3e0-11e9-bbe5-00163e09c2be`: the mount point for the NAS file system.
    To mount different volumes in a NAS file system to different pods, create a PVC for each pod.


## Mount a dynamic NAS volume in the filesystem mode

**Note:** By default, if you delete a NAS volume mounted in the filesystem mode, the system retains the NAS file system and the mount point. If you want the system to delete the NAS file system and the mount point when you delete the NAS volume, set reclaimPolicy to Delete and deleteVolume to **true** in the StorageClass.

We recommend that you mount a NAS volume in the filesystem mode if you want the system to automatically create or delete the NAS file system and the mount point.

You can perform the following steps:

1.  Configure a RAM policy and attach the policy to a RAM role.

    In the filesystem mode, you can enable automatic creation and deletion of NAS file systems and mount points. To enable this feature, grant the related permissions to csi-nasprovisioner. You can use the following RAM policy to grant a RAM role the minimum permission first.

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

    You can perform either of the following operations to attach the RAM policy to a RAM role:

    -   Add the preceding NAS permissions to the custom policy settings of the master RAM role. For more information, see [ACK default roles](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/ACK default roles.md).

        ![Attach the custom policy to the master RAM role](../images/p69183.png)

    -   Create a RAM user and attach the RAM policy to the RAM user. After you attach the policy, you can obtain an AccessKey pair. Enter the AccessKey ID and the AccessKey secret in the env section of csi-nasprovisioner. For more information, see [ACK default roles](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/ACK default roles.md).

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

    1.  Write the following content to the alicloud-nas-fs.yaml file.

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
        |volumeAs|The mode used for mounting the NAS volume. Valid values:         -   filesystem: The csi-nasprovisioner component automatically creates a NAS file system. Each NAS volume corresponds to one NAS file system.
        -   subpath: The csi-nasprovisioner component automatically creates a subdirectory in a NAS file system. Each NAS volume corresponds to a subdirectory in a NAS file system. |
        |storageType|The type of the NAS file system. Valid values: Performance and Capacity. Default value: Performance.|
        |zoneId|The ID of the zone to which the NAS file system belongs.|
        |vpcId|The ID of the virtual private cloud \(VPC\) to which the mount point for the NAS file system belongs.|
        |vSwitchId|The ID of the VSwitch to which the mount point for the NAS file system belongs.|
        |accessGroupName|The permission group to which the mount point for the NAS file system belongs. Default value: DEFAULT\_VPC\_GROUP\_NAME.|
        |deleteVolume|Specifies whether to automatically delete the NAS file system when you delete the NAS volume. To ensure security, specify both deleteVolume and reclaimPolicy.|
        |reclaimPolicy|Specifies the reclaim policy. If you delete the NAS volume, the NAS file system is automatically deleted only if you set deleteVolume to true and reclaimPolicy to Delete.|

    2.  Run the following command to create a StorageClass:

        ```
        kubectl create -f alicloud-nas-fs.yaml
        ```

3.  Create a PVC and a pod, and mount a NAS volume to the pod.

    Write the following content to the `pvc.yaml` and `nginx.yaml` files.

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

    Run the following command to create a PVC and a pod.

    ```
    kubectl create -f pvc.yaml -f nginx.yaml
    ```


In the filesystem mode, the CSI driver automatically creates a NAS file system and a mount point when you create a PVC. If you set deleteVolume to true and reclaimPolicy to Delete, the file system and the mount point are automatically deleted when you delete the PVC.

