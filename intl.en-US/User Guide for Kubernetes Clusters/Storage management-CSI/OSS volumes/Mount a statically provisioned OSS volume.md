---
keyword: [OSS, statically provisioned volume, K8s]
---

# Mount a statically provisioned OSS volume

Object Storage Service \(OSS\) is a secure, cost-effective, and high-durability cloud storage service provided by Alibaba Cloud. OSS allows you to store a large amount of data in the cloud. This topic describes how to mount an OSS bucket as a statically provisioned volume by using YAML.

[Create buckets](/intl.en-US/Quick Start/OSS console/Create buckets.md).

**Note:** If a node and an OSS bucket belong to the same region, you can use the internal endpoint of the OSS bucket when you mount the OSS bucket to the node.

## Mount an OSS bucket as a statically provisioned volume by using kubectl

Before you use kubectl to mount an OSS bucket as a statically provisioned volume, make sure that you can connect to the cluster by using kubectl. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).

**Step 1: Create a static PV and a PVC**

You can create a static persistent volume \(PV\) and a persistent volume claim \(PVC\) by using the following methods:

Method 1: Create a static PV and a PVC by using a Secret

1.  Create a Secret.

    The following YAML template provides an example on how to create a Secret that stores your AccessKey pair:

    ```
    apiVersion: v1
    kind: Secret
    metadata:
      name: oss-secret
      namespace: default
    stringData:
      akId: ***
      akSecret: ***
    ```

    **Note:** The Secret must be created in the same namespace where the application that uses the PV is deployed.

    Replace the values of `akId` and `akSecret` with your AccessKey ID and AccessKey secret.

2.  Create a static PV and a PVC.

    The following YAML template provides an example on how to create a static PV and a PVC:

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: oss-pvc
    spec:
      accessModes:
      - ReadWriteMany
      resources:
        requests:
          storage: 5Gi
    ---
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: oss-csi-pv
    spec:
      capacity:
        storage: 5Gi
      accessModes:
        - ReadWriteMany
      persistentVolumeReclaimPolicy: Retain
      csi:
        driver: ossplugin.csi.alibabacloud.com
        volumeHandle: oss-csi-pv // The value must be the same as the name of the PV. 
        nodePublishSecretRef:
          name: oss-secret
          namespace: default
        volumeAttributes:
          bucket: "oss"
          url: "oss-cn-hangzhou.aliyuncs.com"
          otherOpts: "-o max_stat_cache_size=0 -o allow_other"
          path: "/"
    ```

    |Parameter|Description|
    |---------|-----------|
    |`nodePublishSecretRef`|Specifies that the AccessKey pair is retrieved from a Secret when an OSS bucket is mounted as a PV.|
    |`volumeHandle`|Specifies the name of the PV.|
    |`bucket`|Specifies the OSS bucket to be mounted.|
    |`url`|Specifies the endpoint of the OSS bucket. If the OSS bucket and the node to which the OSS bucket is mounted are deployed in the same region, you can use the internal endpoint of the OSS bucket.|
    |`otherOpts`|You can enter custom parameters in the format of `-o *** -o ***`.|
    |`path`|Specifies the path relative to the root directory of the OSS bucket to be mounted. The default value is /. This parameter is supported by csi-plugin 1.14.8.32-c77e277b-aliyun and later.|


Method 2: Specify an AccessKey pair when you create a PV and a PVC

[Method 1: Create a static PV and a PVC by using a Secret](#p_dum_5ej_hg0) describes how to use a Secret to provide your AccessKey pair to the Container Storage Interface \(CSI\) plug-in. You can also specify an AccessKey pair when you create a static PV. The following YAML template is an example:

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: oss-csi-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  csi:
    driver: ossplugin.csi.alibabacloud.com
    volumeHandle: oss-csi-pv // The value must be the same as the name of the PV. 
    volumeAttributes:
      bucket: "oss"
      url: "oss-cn-hangzhou.aliyuncs.com"
      otherOpts: "-o max_stat_cache_size=0 -o allow_other"
      akId: "***"
      akSecret: "***"
```

Method 3: Configure security token-based authentication when you create a PV and a PVC.

In addition to [Method 1: Create a static PV and a PVC by using a Secret](#p_dum_5ej_hg0) and [Method 2: Specify an AccessKey pair when you create a PV and a PVC](#p_mfp_yr3_ovq), you can also configure security token-based authentication when you create a PV. The following YAML template is an example:

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: oss-csi-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  csi:
    driver: ossplugin.csi.alibabacloud.com
    volumeHandle: oss-csi-pv // The value must be the same as the name of the PV. 
    volumeAttributes:
      bucket: "oss"
      url: "oss-cn-hangzhou.aliyuncs.com"
      otherOpts: "-o max_stat_cache_size=0 -o allow_other"
      authType: "sts"
```

**Step 2: Create an application**

The following YAML template provides an example on how to create an application that is mounted with the OSS bucket:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
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
          - name: oss-pvc
            mountPath: "/data"
      volumes:
        - name: oss-pvc
          persistentVolumeClaim:
            claimName: oss-pvc
```

## Mount an OSS bucket as a statically provisioned volume in the ACK console

**Step 1: Create a PV**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volumes**.

5.  In the upper-right corner of the **Persistent Volumes** page, click **Create**.

6.  In the **Create PV** dialog box, set the following parameters.

    |Parameter|Description|
    |---------|-----------|
    |**PV Type**|You can select Cloud Disk, Apsara File Storage NAS \(NAS\), or OSS. In this example, OSS is selected.|
    |**Volume Name**|The name of the PV. The name must be unique in the namespace. In this example, pv-oss is used.|
    |**Volume Plug-in**|You can select Flexvolume or CSI. In this example, CSI is selected.|
    |**Capacity**|The capacity of the PV.|
    |**Access Mode**|Default value: ReadWriteMany.|
    |**AccessKey ID**|The AccessKey ID that is required for accessing the OSS bucket.|
    |**AccessKey Secret**|The AccessKey secret that is required for accessing the OSS bucket.|
    |**Optional Parameters**|You can enter custom parameters in the format of `-o *** -o ***`.|
    |**Bucket ID**|Enter the name of the OSS bucket that you want to mount. Click **Select Bucket**. In the dialog box that appears, select the OSS bucket that you want to use and click **Select**.|
    |**Endpoint**|Select the endpoint of the OSS bucket:     -   If the OSS bucket and the Elastic Compute Service \(ECS\) instance belong to different regions, select **Public Endpoint**.
    -   If the OSS bucket and the ECS instance belong to the same region, set the value based on the network type of the cluster.
        -   If the cluster uses a virtual private cloud \(VPC\), select **VPC Endpoint**.
        -   If the cluster uses a classic network, select **Internal Endpoint**. |
    |**Label**|Add labels to the PV.|

7.  Click **Create**.


**Step 2: Create a PVC**

1.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volume Claims**.

2.  In the upper-right corner of the **Persistent Volume Claims** page, click **Create**.

3.  In the Create PVC dialog box, set the following parameters.

    |Parameter|Description|
    |---------|-----------|
    |**PVC Type**|You can select Cloud Disk, NAS, or OSS. In this example, OSS is selected.|
    |**Name**|The name of the PVC. The name must be unique in the namespace.|
    |**Allocation Mode**|In this example, Existing Volumes is selected. **Note:** If no PV is created, you can set **Allocation Mode** to **Create Volume**, and set the parameters to create a PV. For more information, see [Step 1: Create a PV](#p_fku_upj_qhs). |
    |**Existing Volumes**|Click **Select PV**. Find the PV that you want to use and click **Select** in the Actions column.|
    |**Capacity**|The capacity of the PVC. **Note:** The capacity of the PVC cannot exceed the capacity of the disk. |

4.  Click **Create**.

    After the PVC is created, you can find the PVC named csi-oss-pvc in the list of Persistent Volume Claims, and the PVC is associated with the PV that you specified.


**Step 3: Create an application**

1.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

2.  In the upper-right corner of the **Deployments** page, click **Create from Image**.

3.  Set the parameters that are required for creating an application.

    This example shows how to set the volume parameters. For more information about other parameters, see [Use a Deployment to create a stateless application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Use a Deployment to create a stateless application.md).

    You can add local volumes and cloud volumes.

    -   **Add Local Storage**: You can select HostPath, ConfigMap, Secret, or EmptyDir from the PV Type drop-down list. Then, set the Mount Source and Container Path parameters to mount the volume to a container path. For more information, see [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE).
    -   **Add PVC**: You can add cloud volumes.
    In this example, csi-oss-pvc is selected as the Mount Source and mounted to the /tmp path in the container.

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4606191261/p69158.png)

4.  Set other parameters and click **Create**.

    After you create the application, you can use the OSS volume to store application data.


