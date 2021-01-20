---
keyword: [statically provisioned volumes, NAS, FlexVolume]
---

# Statically provisioned NAS volumes

You can use the FlexVolume plug-in provided by Alibaba Cloud to use Apsara File Storage NAS in Container Service for Kubernetes \(ACK\). This topic describes how to use a statically provisioned NAS volume.

-   Upgrade the FlexVolume plug-in to the latest version.
-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

The FlexVolume plug-in allows you to use a NAS file system as a volume. You can also use a NAS file system to create a PV and a PVC.

The following parameters are included:

-   server: the mount target of the NAS volume.
-   path: the mounted directory in the NAS file system. You can specify a sub-directory as a volume. If no sub-directory exists, the system automatically creates and mounts a sub-directory. To specify a directory in a NAS Extreme file system, the directory must start with /share.
-   vers: the version of the NFS protocol. Versions 3 and 4.0 are supported. By default, version 3 is used. We recommend that you use version 3. NAS Extreme file systems support only NFS version 3.
-   mode: the access permissions on the mounted directory. If the root directory of the NAS file system is specified as the mounted directory, you cannot modify the access permissions. If you set the mode parameter for a directory that stores a large amount of data, the process of mounting the directory to a cluster may require an excessive amount of time or even fail.
-   options: the mount parameter. If you do not set this parameter, the default values are `nolock,tcp,noresvport` in version 3 and `noresvport` in version 4.0.

## Use a NAS file system as a volume

You can set the volumes field to specify a NAS file system as a volume of pods. Use a nas-deploy.yaml file to create a pod.

1.  Create the nas-deploy.yaml file and copy the following content to the file.

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nas-static
      labels:
        app: nginx
    spec:
      replicas: 1
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
              - name: nas1
                mountPath: "/data"
          volumes:
          - name: "nas1"
            flexVolume:
              driver: "alicloud/nas"
              options:
                server: "0cd8b4a576-grs79.cn-hangzhou.nas.aliyuncs.com"
                path: "/k8s"
                vers: "3"
                options: "nolock,tcp,noresvport"
    ```

2.  Run the following command to create a pod:

    ```
    kubectl apply -f nas-deploy.yaml
    ```


## Use a NAS file system to create a PV and a PVC

You can use a NAS file system to create a PV and a PVC and associate them with a pod.

1.  Create a PV.

    You can create a PV in the ACK console or by using a YAML file.

    -   Create a PV by using a YAML file.

        Use the following nas-pv.yaml file to create a PV.

        ```
        apiVersion: v1
        kind: PersistentVolume
        metadata:
          name: pv-nas
        spec:
          capacity:
            storage: 5Gi
          storageClassName: nas
          accessModes:
            - ReadWriteMany
          flexVolume:
            driver: "alicloud/nas"
            options:
              server: "0cd8b4a576-uih75.cn-hangzhou.nas.aliyuncs.com"
              path: "/k8s"
              vers: "3"
              options: "nolock,tcp,noresvport"
        ```

    -   Create a PV in the ACK console
        1.  Log on to the [ACK console](https://cs.console.aliyun.com).
        2.  In the left-side navigation pane, click **Clusters**.
        3.  On the Clusters page, find the cluster that you want to manage and click **Details** in the **Actions** column of the cluster.
        4.  The Cluster Information page appears. In the left-side navigation pane, click **Persistent Volumes**. The Persistent Volume Claims tab appears.
        5.  Click the **Persistent Volumes** tab and click **Create**.
        6.  In the Create PV dialog box, set the parameters.

            |Parameter|Description|
            |---------|-----------|
            |**PV Type**|In this example, NAS is selected.|
            |**Name**|The name of the PV. The name must be unique in the cluster. In this example, pv-nas is used.|
            |**Volume Plug-in**|In this example, FlexVolume is selected. For more information about volume plug-ins, see [Differences between Flexvolume and CSI](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Volume plug-ins.md).|
            |**Capacity**|The capacity of the PV. The capacity of the PV cannot exceed that of the NAS file system.|
            |**Access Mode**|By default, ReadWriteMany is selected.|
            |**Mount Target Domain Name**|The domain name of the mount target that is used to mount the NAS file system to the cluster.|
            |**Subdirectory**|Enter a sub-directory in the NAS file system. The sub-directory must start with a forward slash \(/\). If this parameter is set, the PV will be mounted to the sub-directory.             -   If the specified sub-directory does not exist, the system automatically creates this sub-directory.
            -   If you do not set this parameter, the root directory of the NAS file system is mounted.
            -   To specify a sub-directory in a NAS Extreme file system, the sub-directory must start with /share. |
            |**Permissions**|The access permissions on the mounted directory. For example, you can set this parameter to 755, 644, or 777.**Note:**

            -   You can set access permissions on only sub-directories.
            -   We recommend that you do not set this parameter when the mounted directory contains a large number of files. Otherwise, it may take a long time to run the chmod command.
If the mounted directory is a sub-directory of the NAS file system, the Permissions parameter is optional.

            -   By default, the original permissions are used.
            -   Take note of the following requirements when you set the permissions:
                -   For FlexVolume versions earlier than v1.14.6.15-8d3b7e7-aliyun, use the recursive mode when you change permission settings. The permissions on all files and directories under the mounted directory will be changed.
                -   For FlexVolume v1.14.6.15-8d3b7e7-aliyun and later, set the **chmod \(Change Mode\)** parameter to define how permission changes are applied. |
            |**chmod \(Change Mode\)**|The change mode of access permissions. Valid values: Non-recursive and Recursive.            -   Non-recursive: The permission changes apply to only the mount directory. The subdirectories and files in the mount directory are not affected.
            -   Recursive mode: The permission changes apply to the mounted directory, the subdirectories, and the included files.

**Note:** If you select the recursive mode for a mounted directory that contains a large number of files, the process of running the chmod command may require an excessive amount of time. The mount or unmount operation may fail. Exercise caution when you perform this operation. |
            |**Version**|The version of the NFS protocol. We recommend that you use version 3. NAS Extreme file systems support only NFS version 3.|
            |**Labels**|Add labels to the PV.|

        7.  Click **Create**.
2.  Create a PVC.

    Use the following nas-pvc.yaml file to create a PVC.

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: pvc-nas
    spec:
      accessModes:
        - ReadWriteMany
      storageClassName: nas
      resources:
        requests:
          storage: 5Gi
    ```

3.  Create a pod.

    Use the following nas-pod.yaml file to create a pod.

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nas-static
      labels:
        app: nginx
    spec:
      replicas: 1
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
                mountPath: /data
          volumes:
            - name: pvc-nas
              persistentVolumeClaim:
                claimName: pvc-nas
    ```


[Use NAS volumes](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/NAS volumes/Use NAS volumes.md)

