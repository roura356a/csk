# Use the CLI to dynamically provision a disk as a PV

To dynamically provision a disk as a persistent volume \(PV\), you must manually create a StorageClass, and set the storageClassName field in a persistent volume claim \(PVC\) to specify the disk type.

## Create a StorageClass with a specified zone ID \(zoneId\)

1.  Create a storage-class.yaml file and copy the following content to the file:

    ```
    kind: StorageClass
    apiVersion: storage.k8s.io/v1beta1
    metadata:
      name: alicloud-disk-ssd-hangzhou-b
    provisioner: alicloud/disk
    parameters:
      type: cloud_ssd
      regionid: cn-hangzhou
      zoneId: cn-hangzhou-b
    reclaimPolicy: Retain
    ```

    The following table describes the parameters that are included in the file.

    |Parameter|Description|
    |---------|-----------|
    |`provisioner`|Set the value to alicloud/disk. This is a volume plug-in used to provision disks.|
    |`type`|The disk type. Valid values include cloud\_efficiency, cloud\_ssd, cloud\_essd, and available. If you set this parameter to available, the system attempts to create a disk in the following order: enhanced SSD \(ESSD\), standard SSD, and ultra disk. The system keeps trying until a disk is created.|
    |`regionid`|The region where you want to create the disk.|
    |`reclaimPolicy`|The policy to reclaim a disk. By default, this parameter is set to Delete. You can also set this parameter to Retain. If you require high data security, we recommend that you set this parameter to Retain to avoid data loss caused by user errors.|
    |`zoneId`|The zone where you want to create the disk. For a multi-zone cluster, you can specify multiple zones. Example:

    ```
zoneId: cn-hangzhou-a,cn-hangzhou-b,cn-hangzhou-c
    ``` |
    |`encrypted`|This parameter is optional. This parameter specifies whether the disk is encrypted. By default, the value is set to false to specify that the disk is not encrypted.|

2.  Run the following command to create a StorageClass:

    ```
    kubectl apply -f storage-class.yaml
    ```


## Create a StorageClass in WaitForFirstConsumer mode

```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
   name: alicloud-disk-topology-ssd
provisioner: alicloud/disk
parameters:
    type: cloud_ssd
reclaimPolicy: Retain
volumeBindingMode: WaitForFirstConsumer
```

**Note:**

-   If you do not create a StorageClass in WaitForFirstConsumer mode and the zoneid parameter is not set, a PV is created in the zone where the Disk-Controller component is deployed.
-   If you do not create a StorageClass in WaitForFirstConsumer mode but the zoneid parameter is set, the system attempts to create a PV in the specified zones based on the round-robin algorithm.
-   If you create a StorageClass in WaitForFirstConsumer mode, a disk is created for the node to which the pod that consumes the PVC is scheduled. The cloud disk is created in the zone of the scheduled pod.

## Create a PVC

```
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: disk-ssd
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: alicloud-disk-ssd-hangzhou-b
  resources:
    requests:
      storage: 20Gi
---
kind: Pod
apiVersion: v1
metadata:
  name: disk-pod-ssd
spec:
  containers:
  - name: disk-pod
    image: nginx
    volumeMounts:
      - name: disk-pvc
        mountPath: "/mnt"
  restartPolicy: "Never"
  volumes:
    - name: disk-pvc
      persistentVolumeClaim:
        claimName: disk-ssd
```

The following default settings are included:

In a multi-zone cluster, you must manually create a StorageClass to specify the zone where a disk is created.

By default, the following types of StorageClasses are provided for single-zone clusters:

-   alicloud-disk-efficiency: ultra disk.
-   alicloud-disk-ssd: standard SSD.
-   alicloud-disk-essd: ESSD.
-   alicloud-disk-available: a high-availability mode. In this mode, the system first attempts to create a standard SSD. If SSD resources are exhausted, the system attempts to create an ultra disk.

    **Note:** For versions earlier than alicloud-disk-controller v1.14.8.44-c23b62c5-aliyun, the system attempts to create a disk in the following order: ESSD, standard SSD, and ultra disk. The system keeps trying until a disk is created.

-   alicloud-disk-topology: creates a disk in WaitForFirstConsumer mode.

## Create a multi-instance StatefulSet by using a disk

We recommend that you use the volumeClaimTemplates parameter. This allows you to dynamically create multiple PVCs and PVs and associate PVs with PVCs.

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
  replicas: 2
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
          name: web
        volumeMounts:
        - name: disk-ssd
          mountPath: /data
  volumeClaimTemplates:
  - metadata:
      name: disk-ssd
    spec:
      accessModes: [ "ReadWriteOnce" ]
      storageClassName: "alicloud-disk-ssd-hangzhou-b"
      resources:
        requests:
          storage: 20Gi
```

You can also use dynamically provisioned PVs in the Container Service for Kubernetes \(ACK\) console. For more information, see [Use disks to create dynamically provisioned persistent volumes in the ACK console](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Disk volumes/Use disks to create dynamically provisioned persistent volumes in the ACK console.md).

