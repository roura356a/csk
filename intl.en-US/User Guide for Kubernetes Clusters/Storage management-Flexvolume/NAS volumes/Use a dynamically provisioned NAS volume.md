---
keyword: [dynamically provisioned volumes, NAS, FlexVolume]
---

# Use a dynamically provisioned NAS volume

You can create a subdirectory in an Apsara File Storage NAS \(NAS\) file system and map the subdirectory to a dynamically provisioned persistent volume \(PV\) for applications. This topic describes how to mount a dynamically provisioned NAS volume.

-   A Container Service for Kubernetes \(ACK\) cluster is created and the FlexVolume driver is installed in the cluster. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   The alicloud-nas-controller component is deployed in the cluster. For more information, see [Install and upgrade FlexVolume](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Install and upgrade FlexVolume.md).

## Precautions

If the securityContext.fsgroup parameter is set in the application template, kubelet performs the `chmod` or `chown` operation after the volume is mounted, which increases the time consumption.

## Create a dynamically provisioned NAS volume

1.  Configure a StorageClass.

    Sample code:

    ```
    apiVersion: storage.k8s.io/v1
    kind: StorageClass
    metadata:
      name: alicloud-nas
    mountOptions:
    - nolock,tcp,noresvport
    - vers=3
    parameters:
      server: "23a9649583-i****.cn-shenzhen.nas.aliyuncs.com:/nasroot1/"
      driver: flexvolume
    provisioner: alicloud/nas
    reclaimPolicy: Delete
    ```

    |Parameter|Description|
    |---------|-----------|
    |mountOptions|The mount options of the PV. The NAS volume is mounted based on the specified mount options.|
    |server|The list of mount targets that are used to provision the PV. The format is nfsurl1:/path1,nfsurl2:/path2. When multiple servers are specified, the PV provisioned by this StorageClass uses the servers in a round robin manner. For Extreme NAS file systems, the path must start with /share.|
    |driver|FlexVolume and NFS are supported. The default driver is NFS.|
    |reclaimPolicy|The reclaim policy of the PV. We recommend that you set the value to Retain.     -   If you set the value to Delete, the name of the subdirectory mapped to the PV is automatically changed after you delete the PV. For example, path-name is changed to archived-path-name.
    -   If you want to delete the subdirectory in the NAS file system, set `archiveOnDelete` to false in the StorageClass configurations. |

2.  Use the dynamically provisioned NAS volume in a StatefulSet.

    Create a Service and a StatefulSet by using the following sample code:

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
      selector:
        matchLabels:
          app: nginx
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


