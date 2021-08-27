---
keyword: [OSS, statically provisioned volume, K8s]
---

# Mount a statically provisioned OSS volume

Object Storage Service \(OSS\) is a secure, cost-effective, and high-durability cloud storage service provided by Alibaba Cloud. OSS allows you to store a large amount of data in the cloud. This topic describes how to mount an OSS bucket as a statically provisioned volume in the console or by using the CLI.

-   A Container Service for Kubernetes \(ACK\) cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   An OSS bucket is created. For more information, see [Create buckets](/intl.en-US/Quick Start/OSS console/Create buckets.md).

    **Note:** Select the internal endpoint if the OSS bucket and the ECS instance are deployed in the same region.

-   Your machine is connected to the cluster by using kubectl. For more information, see [Step 2: Select a type of cluster credentials](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md).

OSS is a secure, cost-effective, high-capacity, and high-reliability cloud storage service provided by Alibaba Cloud. You can mount an OSS bucket to multiple pods of an ACK cluster. OSS is applicable to the following scenarios:

-   Average requirements on disk I/O.
-   Sharing of data, including configuration files, images, and small video files.

## Precautions

-   OSS buckets do not support dynamically provisioned persistent volumes \(PVs\).
-   kubelet and the OSSFS driver may be restarted when the ACK cluster is upgraded. As a result, the mounted OSS directory becomes unavailable. In this case, you must recreate the pods to which the OSS volume is mounted. You can add health check settings in the YAML file to restart the pods and remount the OSS volume when the OSS directory becomes unavailable.

    **Note:** If the csi-plugin and csi-provisioner that you use are V1.18.8.45 or later, the preceding issue does not occur.


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
    |**PV Type**|You can select Cloud Disk, NAS, or OSS. In this example, OSS is selected.|
    |**Volume Name**|The name of the PV. The name must be unique in the namespace. In this example, pv-oss is entered.|
    |**Volume Plug-in**|You can select Flexvolume or CSI. In this example, CSI is selected.|
    |**Capacity**|The capacity of the PV.|
    |**Access Mode**|Default value: ReadWriteMany.|
    |**Access Certificate**|Select a Secret that is used to access the OSS bucket.     -   **Select Existing Secret**: Select a namespace and a Secret.
    -   **Create Secret**: Set Namespace, Name, AccessKey ID, and AccessKey Secret. |
    |**Optional Parameters**|You can enter custom parameters for the OSS bucket in the following format: `-o *** -o ***`.|
    |**Bucket ID**|Enter the name of the OSS bucket that you want to mount. Click **Select Bucket**. In the dialog box that appears, find the OSS bucket that you want to use and click **Select**.|
    |**Endpoint**|Select the endpoint of the OSS bucket:     -   If the OSS bucket and the Elastic Compute Service \(ECS\) instance belong to different regions, select **Public Endpoint**.
    -   If the OSS bucket and the ECS instance belong to the same region, select **Internal Endpoint**. |
    |**Label**|Add labels to the PV.|

7.  Click **Create**.


**Step 2: Create a PVC**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volume Claims**.

5.  In the upper-right corner of the **Persistent Volume Claims** page, click **Create**.

6.  In the Create PVC dialog box, set the following parameters.

    |Parameter|Description|
    |---------|-----------|
    |**PVC Type**|You can select Cloud Disk, NAS, or OSS. In this example, OSS is selected.|
    |**Name**|The name of the persistent volume claim \(PVC\). The name must be unique in the cluster.|
    |**Allocation Mode**|In this example, Existing Volumes is selected. **Note:** If no PV is created, you can set **Allocation Mode** to **Create Volume** and then configure a PV. For more information, see [Step 1: Create a PV](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/OSS volumes/Mount a statically provisioned OSS volume.md). |
    |**Existing Volumes**|Click **Select PV**. Find the PV that you want to use and click **Select** in the Actions column.|
    |**Capacity**|The capacity of the selected PV. **Note:** The capacity of a PV cannot be greater than the capacity of the OSS bucket that is associated with the PV. |

7.  Click **Create**.

    After the PVC is created, you can find the PVC named **csi-oss-pvc** in the list of PVCs. The PV is bound to the PVC.


**Step 3: Create an application**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  In the upper-right corner of the **Deployments** page, click **Create from Image**.

6.  Set the application parameters.

    This example shows how to set the volume parameters. For more information about other parameters, see [Create a stateless application by using a Deployment](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Create a stateless application by using a Deployment.md).

    You can add local volumes and cloud volumes.

    -   **Add Local Storage**: You can select HostPath, ConfigMap, Secret, or EmptyDir from the PV Type drop-down list. Then, set the Mount Source and Container Path parameters to mount the volume to a container path. For more information, see [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE).
    -   **Add PVC**: You can add cloud volumes.
    In this example, an OSS volume is selected and mounted to the /tmp path in the container.

    ![](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/4606191261/p69158.png)

7.  Set other parameters and click **Create**.

    After the application is created, you can use the OSS volume to store application data.


## Mount an OSS bucket as a statically provisioned volume by using kubectl

**Step 1: Create a statically provisioned PV and a PVC**

You can create a statically provisioned PV and a PVC by using the following methods:

Method 1: Create a statically provisioned PV and a PVC by using a Secret

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

    **Note:** The Secret must be created in the namespace where the application that uses the PV is deployed.

    Replace the values of `akId` and `akSecret` with your AccessKey ID and AccessKey secret.

2.  Run the following command to create a statically provisioned PV:

    ```
    kubectl create -f pv-oss.yaml
    ```

    The following pv-oss.yaml file is used to create the statically provisioned PV:

    ```
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: pv-oss
      labels:
        alicloud-pvname: pv-oss
    spec:
      capacity:
        storage: 5Gi
      accessModes:
        - ReadWriteMany
      persistentVolumeReclaimPolicy: Retain
      csi:
        driver: ossplugin.csi.alibabacloud.com
        volumeHandle: pv-oss // The specified value must be the same as the name of the PV. 
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
    |name|The name of the PV|
    |labels|The labels that you want to add to the PV|
    |storage|The available storage of the OSS bucket|
    |accessModes|The access mode of the PV|
    |persistentVolumeReclaimPolicy|The policy for reclaiming the PV|
    |driver|The type of volume driver that you want to use. This parameter is set to ossplugin.csi.alibabacloud.com, which indicates that the OSS CSI plug-in is used.|
    |nodePublishSecretRef|The Secret from which the AccessKey pair is retrieved when an OSS bucket is mounted as a PV|
    |volumeHandle|The name of the PV|
    |bucket|The OSS bucket that you want to mount. You can mount only OSS buckets to clusters. The subdirectories or files in an OSS bucket cannot be mounted to an ACK cluster.|
    |url|The endpoint of the OSS bucket to be mounted:     -   If the node and the OSS bucket belong to the same region, use the internal endpoint of the OSS bucket.
    -   If the node and the OSS bucket belong to different regions, use the public endpoint of the OSS bucket.
    -   You cannot use a virtual private cloud \(VPC\) endpoint. |
    |otherOpts|You can enter custom parameters in the format of `-o *** -o ***`.|
    |path|The path relative to the root directory of the OSS bucket to be mounted. The default value is /. This parameter is supported by csi-plugin 1.14.8.32-c77e277b-aliyun and later.|

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).
    2.  In the left-side navigation pane of the ACK console, click **Clusters**.
    3.  On the Clusters page, find the cluster that you want to manage and click the name or click **Details** in the **Actions** column.
    4.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volumes**. You can view the created PV on the Persistent Volumes page.
3.  Run the following command to create a PVC:

    ```
    kubectl create -f pvc-oss.yaml
    ```

    The following pvc-oss.yaml file is used to create the PVC:

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: pvc-oss
    spec:
      accessModes:
        - ReadWriteMany
      resources:
        requests:
          storage: 5Gi
      selector:
        matchLabels:
          alicloud-pvname: pv-oss
    ```

    |Parameter|Description|
    |---------|-----------|
    |`name`|The name of the PVC|
    |`accessModes`|The access mode of the PVC|
    |`storage`|The capacity claimed by the PVC. The claimed capacity cannot exceed the capacity of the PV.|
    |`alicloud-pvname`|The labels that are used to select and bind a PV to the PVC. The labels must be the same as those of the PV to be bound to the PVC.|

    In the left-side navigation pane, choose **Volumes** \> **Persistent Volume Claims**. On the Persistent Volume Claims page, you can view the created PVC.


Method 2: Specify an AccessKey pair when you create a PV and a PVC

[Method 1: Create a statically provisioned PV and a PVC by using a Secret](#p_dum_5ej_hg0) describes how to specify a Secret that stores an AccessKey pair for the CSI plug-in.

You can also run the following command to specify the AccessKey pair in the PV configurations:

```
kubectl create -f pv-accesskey.yaml
```

The following pv-accesskey.yaml file provides an example on how to specify an AccessKey pair in the PV configurations:

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-oss
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  csi:
    driver: ossplugin.csi.alibabacloud.com
    volumeHandle: pv-oss // The specified value must be the same as the name of the PV. 
    volumeAttributes:
      bucket: "oss"
      url: "oss-cn-hangzhou.aliyuncs.com"
      otherOpts: "-o max_stat_cache_size=0 -o allow_other"
      akId: "***"
      akSecret: "***"
```

Method 3: Configure security token-based authentication when you create a PV and a PVC.

In addition to [Method 1: Create a statically provisioned PV and a PVC by using a Secret](#p_dum_5ej_hg0) and [Method 2: Specify an AccessKey pair when you create a PV and a PVC](#p_mfp_yr3_ovq),

you can also run the following command to configure security token-based authentication:

```
kubectl create -f pv-sts.yaml
```

The following pv-sts.yaml file provides an example on how to configure security token-based authentication:

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-oss
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  csi:
    driver: ossplugin.csi.alibabacloud.com
    volumeHandle: pv-oss // The specified value must be the same as the name of the PV. 
    volumeAttributes:
      bucket: "oss"
      url: "oss-cn-hangzhou.aliyuncs.com"
      otherOpts: "-o max_stat_cache_size=0 -o allow_other"
      authType: "sts"
```

**Step 2: Create an application**

Create an application named oss-static and mount the PVC to the application.

Run the following command to create an oss-static.yaml file:

```
kubectl create -f oss-static.yaml
```

The following oss-static.yaml file is used to create the application:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: oss-static
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
          - name: pvc-oss
            mountPath: "/data"
          - name: pvc-oss
            mountPath: "/data1"
        livenessProbe:
          exec:
            command:
            - sh
            - -c
            - cd /data
          initialDelaySeconds: 30
          periodSeconds: 30
      volumes:
        - name: pvc-oss
          persistentVolumeClaim:
            claimName: pvc-oss
```

-   `livenessProbe`: health checks settings. For more information, see [Overview](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/OSS volumes/Overview.md).
-   `mountPath`: the path where the OSS bucket is mounted in the container.
-   `claimName`: the name of the PVC, which is used to associate the application with the PVC.

## Verify that data can be persisted in the OSS bucket

1.  View the pod that runs the oss-static application and the files in the OSS bucket.

    1.  Run the following command to query the pod that runs the oss-static application:

        ```
        kubectl get pod
        ```

        Expected output:

        ```
        NAME                             READY   STATUS    RESTARTS   AGE
        oss-static-66fbb85b67-d****      1/1     Running   0          1h
        ```

    2.  Run the following command to query files in the /data path:

        ```
         kubectl exec oss-static-66fbb85b67-d**** ls /data | grep tmpfile
        ```

        No output is returned. This indicates that no file is stored in the /data path.

2.  Run the following command to create a file named static in the /data path:

    ```
    kubectl exec oss-static-66fbb85b67-d**** touch /data/tmpfile
    ```

3.  Run the following command to query files in the /data path:

    ```
    kubectl exec oss-static-66fbb85b67-d**** ls /data | grep tmpfile
    ```

    Expected output:

    ```
    tmpfile
    ```

4.  Run the following command to delete the `oss-static-66fbb85b67-d****` pod:

    ```
    kubectl delete pod oss-static-66fbb85b67-d****
    ```

    Expected output:

    ```
    pod "oss-static-66fbb85b67-d****" deleted
    ```

5.  Open another kubectl CLI and run the following command to view how the pod is deleted and recreated:

    ```
    kubectl get pod -w -l app=nginx
    ```

    Expected output:

    ```
    NAME                            READY   STATUS            RESTARTS   AGE
    nginx-static-78c7dcb9d7-g****   2/2     Running           0          50s
    nginx-static-78c7dcb9d7-g****   2/2     Terminating       0          72s
    nginx-static-78c7dcb9d7-h****   0/2     Pending           0          0s
    nginx-static-78c7dcb9d7-h****   0/2     Pending           0          0s
    nginx-static-78c7dcb9d7-h****   0/2     Init:0/1          0          0s
    nginx-static-78c7dcb9d7-g****   0/2     Terminating       0          73s
    nginx-static-78c7dcb9d7-h****   0/2     Init:0/1          0          5s
    nginx-static-78c7dcb9d7-g****   0/2     Terminating       0          78s
    nginx-static-78c7dcb9d7-g****   0/2     Terminating       0          78s
    nginx-static-78c7dcb9d7-h****   0/2     PodInitializing   0          6s
    nginx-static-78c7dcb9d7-h****   2/2     Running           0          8s
    ```

6.  Verify that the file still exists after the pod is deleted.

    1.  Run the following command to query the pod that is recreated.

        ```
        kubectl get pod
        ```

        Expected output:

        ```
        NAME                             READY   STATUS    RESTARTS   AGE
        oss-static-66fbb85b67-z****      1/1     Running   0          40s
        ```

    2.  Run the following command to query files in the /data path:

        ```
        kubectl exec oss-static-66fbb85b67-z**** ls /data | grep tmpfile
        ```

        Expected output:

        ```
        tmpfile
        ```

        The temfile file still exists in the /data path. This indicates that data is persisted to the OSS bucket.


