# Dynamic NAS volumes

This topic describes how to create a dynamic NAS volume by creating a subdirectory in a NAS file system and mapping the subdirectory to a dynamic persistent volume \(PV\) for applications.

The FlexVolume driver is installed in a Container Service for Kubernetes \(ACK\) cluster. By default, the FlexVolume driver is installed for ACK clusters.

alicloud-nas-controller is deployed. For more information, see [Install plug-ins](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Install plug-ins.md).

## Create a dynamic NAS volume

1.  Configure a StorageClass.

    The following code block is an example of the StorageClass:

    ```
    apiVersion: storage.k8s.io/v1
    kind: StorageClass
    metadata:
      name: alicloud-nas
    mountOptions:
    - nolock,tcp,noresvport
    - vers=3
    parameters:
      server: "23a9649583-iaq37.cn-shenzhen.nas.aliyuncs.com:/nasroot1/"
      driver: flexvolume
    provisioner: alicloud/nas
    reclaimPolicy: Delete
    ```

    **Note:**

    -   mountOptions: the mount options of the PV. The NAS volume is mounted based on the specified mount options.
    -   server: the list of mount targets that are used by the PV. The format is nfsurl1:/path1,nfsurl2:/path2. When multiple servers are configured, the PV provisioned by this StorageClass uses the servers in a round robin manner. For NAS file systems of Extreme type, the path must start with /share.
    -   driver: supports the FlexVolume and NFS drivers. The default driver is NFS.
    -   reclaimPolicy: the reclaim policy of the PV. We recommend that you set this parameter to Retain.
        -   If you set this parameter to Delete, the name of the subdirectory mapped to the PV is automatically changed after you delete the PV. For example, path-name is changed to archived-path-name.
        -   If you want to delete the subdirectory in the NAS file system, set `archiveOnDelete` to false in the StorageClass.
2.  Use the dynamic NAS volume.

    ```
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      ports:
      - port: 80
        name: web
      clusterIP: None
      selector:
        app: nginx
    ---
    apiVersion: apps/v1
    kind: StatefulSet
    metadata:
      name: web
    spec:
      serviceName: "nginx"
      replicas: 5
      volumeClaimTemplates:
      - metadata:
          name: html
        spec:
          accessModes:
            - ReadWriteOnce
          storageClassName: alicloud-nas
          resources:
            requests:
              storage: 2Gi
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:alpine
            volumeMounts:
            - mountPath: "/data"
              name: html
    ```


