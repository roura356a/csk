# Use Alibaba Cloud cloud disk volumes {#concept_l5p_qvs_vdb .concept}

You can use Alibaba Cloud cloud disk volumes in a Kubernetes cluster of Alibaba Cloud Container Service.

You can mount an Alibaba Cloud cloud disk to a Kubernetes cluster by using the following two methods:

-   [Static volumes](#section_kt5_yvs_vdb)

    You can use a static cloud disk volume in either of the following ways:

    -   [Use a cloud disk through a volume.](#ul_jt5_yvs_vdb)
    -   [Use a cloud disk through a PV and PVC.](#ul_jt5_yvs_vdb)
-   [Dynamic volumes](#section_t55_yvs_vdb)

**Note:** Depending on the type of cloud disk you create, the following requirements must be met:

-   The minimum capacity of a basic cloud disk is 5 GiB.
-   The minimum capacity of an Ultra disk is 20 GiB.
-   The minimum capacity of an SSD disk is 20 GiB.

## Static volumes {#section_kt5_yvs_vdb .section}

You can use an Alibaba Cloud cloud disk through a volume or through a PV and PVC.

**Prerequisites**

You have created a cloud disk in the ECS console. For more information, see [Create a cloud disk](../../../../../reseller.en-US/User Guide/Cloud disks/Create a cloud disk.md#).

**Limits**

-   A cloud disk is a non-shared storage device and can be mounted to only one pod.
-   You must have created a cloud disk and obtained the disk ID before using the cloud disk volume. For more information, see [Create a cloud disk](../../../../../reseller.en-US/User Guide/Cloud disks/Create a cloud disk.md#).
-   The volumeId parameter indicates the ID of a mounted cloud disk. The volume name and PV name must be the same as the value of the volumeId parameter.
-   In a Kubernetes cluster, a cloud disk can be mounted only to a node that resides in the same zone as the cloud disk.
-   Only Pay-As-You-Go cloud disks can be mounted. In a Kubernetes cluster, the ECS instance billing method can be changed to Subscription, but the cloud disk billing method cannot be changed to Subscription. Otherwise, the cloud disks will fail to be mounted.

**Use a cloud disk through a volume**

Use the following disk-deploy.yaml file to create a pod:

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: nginx-disk-deploy
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx-flexvolume-disk
        image: nginx
        volumeMounts:
          - name: "d-bp1j17ifxfasvts3tf40"
            mountPath: "/data"
      volumes:
        - name: "d-bp1j17ifxfasvts3tf40"
          flexVolume:
            driver: "alicloud/disk"
            fsType: "ext4"
            options:
              volumeId: "d-bp1j17ifxfasvts3tf40"
```

**Use a cloud disk through a PV and PVC**

**Step 1: Create a cloud disk PV**

You can create a cloud disk PV in the Container Service console or by using a YAML file.

**Create a PV by using a YAML file**

Use the following `disk-pv.yaml` file to create a PV:

**Note:** The PV name must be the same as the cloud disk ID.

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: d-bp1j17ifxfasvts3tf40
  labels:
    failure-domain.beta.kubernetes.io/zone: cn-hangzhou-b
    failure-domain.beta.kubernetes.io/region: cn-hangzhou
spec:
  capacity:
    storage: 20Gi
  storageClassName: disk
  accessModes:
    - ReadWriteOnce
  flexVolume:
    driver: "alicloud/disk"
    fsType: "ext4"
    options:
      volumeId: "d-bp1j17ifxfasvts3tf40"
```

**Create a cloud disk volume in the Container Service console**

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Kubernetes, choose **Clusters** \> **Volumes**.
3.  Select the target cluster and then click **Create** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16726/154821426010672_en-US.png)

4.  In the displayed dialog box, set the volume parameters.

    -   **Storage type**: Cloud Disk is used in this example.
    -   **Access Mode**: By default, it is set to ReadWriteOnce.
    -   **Cloud Disk ID**: We recommend that you select a cloud disk that is in the same region and zone as the cluster.
    -   **File System Type**: Select a data type for the data to be stored. The available data types include ext4, ext3, xfs, and vfat. The default setting is ext4.
    -   **Tag**: Add tags to the volume.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16726/154821426010673_en-US.png)

5.  Click **Create**.

**Step 2: Create a PVC**

Use the following `disk-pvc.yaml` file to create a PVC:

```
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-disk
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: disk
  resources:
    requests:
      storage: 20Gi
```

**Step 3: Create a pod**

Use the following disk-pod.yaml file to create a pod:

```
apiVersion: v1
kind: Pod
metadata:
  name: "flexvolume-alicloud-example"
spec:
  containers:
    - name: "nginx"
      image: "nginx"
      volumeMounts:
        - name: pvc-disk
          mountPath: "/data"
  volumes:
  - name: pvc-disk
    persistentVolumeClaim:
      claimName: pvc-disk
```

## Dynamic volumes {#section_t55_yvs_vdb .section}

To use a dynamic volume, you need to manually create a StorageClass, and specify a cloud disk type through storageClassName in a PVC.

**Create a StorageClass**

```
kind: StorageClass
apiVersion: storage.k8s.io/v1beta1
metadata:
  name: alicloud-disk-ssd-hangzhou-b
provisioner: alicloud/disk
parameters:
  type: cloud_ssd
  regionid: cn-hangzhou
  zoneid: cn-hangzhou-b
reclaimPolicy: Retain
```

Parameter setting:

-   provisioner: Set this parameter to alicloud/disk to indicate that the StorageClass creates a cloud disk by using the provisioner plugin of Alibaba Cloud cloud disks.
-   type: Specify the type of a cloud disk by using one the following values: cloud, cloud\_efficiency, cloud\_ssd, and available. If you set this parameter to available, the system will cycle through cloud\_efficiency, cloud\_ssd, and cloud in order until one of them takes effect.
-   regionid: Set the region in which you want to create a cloud disk.
-   reclaimPolicy: Set the policy to reclaim a cloud disk. The default setting is Delete. You can also set this parameter to Retain.
-   zoneid: Set the zone in which you want to create a cloud disk.

    **Note:** If you want to create cloud disks in multiple zones, you can set multiple values for the zoneid parameter, for example,

    ```
    zoneid: cn-hangzhou-a,cn-hangzhou-b,cn-hangzhou-c
    ```

-   encrypted: \(optional\) Set whether to encrypt a cloud disk. The default value is false. That is, a cloud disk will not be encrypted.

**Create a service**

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

**Default options**

By default, Kubernetes clusters provide the following StorageClasses that can be used in the single-zone clusters:

-   alicloud-disk-common, namely, a basic cloud disk.
-   alicloud-disk-efficiency, namely, an Ultra disk.
-   alicloud-disk-ssd, namely, an SSD disk.
-   alicloud-disk-available: This StorageClass provides a systematic method of disk selection. Specifically, the system first attempts to create an Ultra disk. If the Ultra disks in the specified zone are sold out, the system tries to create an SSD disk. If the SSD disks are sold out, the system tries to create a basic cloud disk.

**Create a multi-instance StatefulSet by using a cloud disk**

We recommend that you create a multi-instance StatefulSet through volumeClaimTemplates so that you can dynamically create multiple PVCs and PVs, and connect the PVCs and PVs together.

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
apiVersion: apps/v1beta2
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
      storageClassName: "alicloud-disk-ssd"
      resources:
        requests:
          storage: 20Gi
```

