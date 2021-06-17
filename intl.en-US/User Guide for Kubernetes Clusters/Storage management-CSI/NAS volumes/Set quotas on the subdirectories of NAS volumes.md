---
keyword: [subdirectories of NAS volumes, quota limits]
---

# Set quotas on the subdirectories of NAS volumes

You can set quotas to manage resource allocation and improve the overall resource utilization. Container Service for Kubernetes \(ACK\) allows you to use the CSI plug-in to set quotas on the subdirectories of Apsara File Storage NAS volumes. This topic describes how to set quotas on the subdirectories of NAS volumes.

-   The image version of csi-plugin is V1.18.8.45 or later. For more information about csi-plugin versions, see [csi-plugin](/intl.en-US/Release notes/System Component change Records/Storage/csi-plugin.md).
-   The NAS volume is mounted by using a subdirectory.

## Limits

-   Only NAS Capacity file systems support quota limits. For more information about the types of NAS file systems, see [General-purpose NAS file systems]().
-   Quotas can be set only for volumes that are mounted by using subdirectories.
-   Quota limits are supported in all regions except the China \(Hohhot\) and China \(Ulanqab\) regions.
-   For each file system, you can configure quotas only on a maximum of 10 directories.
    -   You can set an enforcement quota on a directory. If the quota is exceeded, you cannot write data to the directory. The write operations include the operations that are used to increase the length of files, create files, subdirectories, and special files, and move files to another directory. An IOError error occurs at the frontend.
    -   To avoid unexpected errors, use caution when you set enforcement quotas on critical directories.
    -   A specific period of time is required before an enforcement quota is enabled or disabled due to asynchronous execution at the backend. In most cases, the time period ranges from 5 to 15 minutes.

## Examples

1.  Create a StorageClass that uses a subdirectory of a NAS file system to provision volumes.

    In this example, the following template is used:

    ```
    apiVersion: storage.k8s.io/v1
    kind: StorageClass
    metadata:
      name: alicloud-nas-sp8
    mountOptions:
      - nolock,tcp,noresvport
      - vers=3
    parameters:
      volumeAs: subpath
      server: "xxx.cn-hangzhou.nas.aliyuncs.com:/"
      archiveOnDelete: "false"
      path: "/abc"
      volumeCapacity: "true"
    provisioner: nasplugin.csi.alibabacloud.com
    reclaimPolicy: Delete
    allowVolumeExpansion: "true"
    ```

    |Parameter|Description|
    |---------|-----------|
    |mountOptions|Set the options parameter and Network File System \(NFS\) version in the mountOptions field.|
    |volumeAs|You can select subpath or filesystem. subpath specifies that a subdirectory is mounted on the cluster while filesystem specifies that a file system is mounted on the cluster.|
    |server|When you mount a subdirectory of the NAS file system as a persistent volume \(PV\), this parameter specifies the mount target of the NAS file system.|
    |archiveOnDelete|This parameter specifies whether to delete the backend storage when reclaimPolicy is set to Delete. NAS is a shared storage service. You must set both reclaimPolicy and deleteVolume to ensure data security. Default value: true.|
    |path|The subdirectory of the NAS file system that is mounted. If you mount an Extreme NAS file system, the path must start with /share.|
    |volumeCapacity|This parameter specifies whether to set a quota. Valid values: true and false.|
    |provisioner|The provisioner of the PV that is provided by ACK.|
    |reclaimPolicy|The policy that is used to reclaim the PV. Valid values:    -   Retain: retains the backend storage when the PV and PVC are deleted. The backend storage may be cloud disks.
    -   Delete: automatically deletes the backend storage and PV when the PVC is deleted. |
    |allowVolumeExpansion|This parameter specifies whether NAS storage volume expansion is supported.|

    **Note:** To create a StorageClass that sets quotas on the subdirectory of a NAS file system, the volumeCapacity parameter must be set to true.

2.  Create a PVC that claims a storage capacity of 20 GiB.

    In this example, the following template is used:

    ```
    kind: PersistentVolumeClaim
    apiVersion: v1
    metadata:
      name: pvc-nas-dynamic-create-subpath8
    spec:
      accessModes:
        - ReadWriteMany
      storageClassName: alicloud-nas-sp8
      resources:
        requests:
          storage: 20Gi
    ```

3.  Create a Deployment that uses the PVC that is created in [Step 2](#step_h63_iin_svv).

    In this example, the following template is used:

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: deployment-nas-dynamic-create8
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
              image: nginx:1.14.2
              ports:
                - containerPort: 80
              volumeMounts:
                - name: pvc-nas-dynamic-create-subpath8
                  mountPath: "/data"
          volumes:
            - name: pvc-nas-dynamic-create-subpath8
              persistentVolumeClaim:
                claimName: pvc-nas-dynamic-create-subpath8
    ```


**Verification**

1.  Run the following command to write 10 GiB of data to the /data directory that is mounted on the Deployment that is created in [Step 3](#step_f1o_9un_06x):

    ```
    dd if=/dev/zero of=10G.txt bs=1M count=10000
    ```

2.  Wait 5 to 15 minutes and then check the quota details of the subdirectory.

    1.  Log on to the [NAS console](https://nas.console.aliyun.com/).

    2.  In the left-side navigation pane, choose **File System** \> **File System List**.

    3.  Select the NAS file system that you have used and choose **More** \> **Quota Management** in the **Operations** column.

    4.  On the Quota Management page, click **Manage Quotas** in the **Operations** column.

        The following figure shows that the subdirectory has a quota limit of 20 GiB. The used storage is 9 GiB.

        ![Quota management](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2281579161/p254150.png)

        When the 20 GiB of storage is used up, the `Disk quota exceeded` error appears if you try to write more data to the subdirectory.

        ![Disk quota](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2281579161/p254667.png)


