---
keyword: [OSS, statically provisioned volume, K8s]
---

# Use a statically provisioned OSS volume

Object Storage Service \(OSS\) is a secure, cost-effective, and high-durability cloud storage service provided by Alibaba Cloud. OSS allows you to store a large amount of data in the cloud. This topic describes how to use a statically provisioned OSS volume in the console or by using a CLI.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   An OSS bucket is created. For more information, see [Create buckets](/intl.en-US/Quick Start/OSS console/Create buckets.md).

    **Note:** If a node and an OSS bucket belong to the same region, you can use the internal endpoint of the OSS bucket to mount the OSS bucket to the node.

-   Your machine is connected to the cluster by using kubectl. For more information, see [Connect to an ACK cluster by using kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to Kubernetes clusters by using kubectl.md).

OSS is a cloud storage service that features large capacity, high security, cost-effectiveness, and high reliability. You can mount an OSS bucket to multiple pods in an ACK cluster. Scenarios:

-   You want to run applications that require high disk I/O.
-   You want to share data, including configuration files, images, and small video files.

## Precaution

-   OSS buckets do not support dynamically provisioned persistent volumes \(PVs\).
-   kubelet and the OSSFS driver may be restarted when the ACK cluster is upgraded. The mounted OSS directory becomes unavailable in this case. To make the OSS directory available, you must restart the pods that use the OSS bucket. We recommend that you add a rule in the YAML file of your application to automatically restart the pods and remount the OSS bucket if the mounted OSS directory becomes unavailable.

    **Note:** If the csi-plugin and csi-provisioner that you use are v1.18.8.45 or later, the preceding issue does not occur.


## Use a NAS file system as a statically provisioned volume in the ACK console

**Step 1: Create a PV**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volumes**.

5.  In the upper-right corner of the **Persistent Volumes** page, click **Create**.

6.  In the **Create PV** dialog box, set the following parameters.

    |Parameter|Description|
    |---------|-----------|
    |**PV Type**|You can select Cloud Disk, NAS, and OSS. In this example, OSS is selected.|
    |**Volume Name**|The name of the PV. The name must be unique in the cluster. In this example, pv-oss is used.|
    |**Volume Plug-in**|You can select Flexvolume or CSI. In this example, CSI is selected.|
    |**Capacity**|The capacity of the persistent volume \(PV\).|
    |**Access Mode**|Default value: ReadWriteMany.|
    |**AccessKey ID**|The AccessKey ID that is used to access the OSS bucket.|
    |**AccessKey Secret**|The AccessKey secret that is used to access the OSS bucket.|
    |**Optional Parameters**|You can enter custom parameters in the format of `-o *** -o ***`.|
    |**Bucket ID**|Enter the name of the OSS bucket that you want to use. Click **Select Bucket**. In the dialog box that appears, select the OSS bucket that you want to use and click **Select**.|
    |**Endpoint**|Select the endpoint of the OSS bucket:     -   If the OSS bucket and the Elastic Compute Service \(ECS\) instance to which you want to mount the OSS bucket belong to different regions, select **Public Endpoint**.
    -   If the OSS bucket and the ECS instance to which you want to mount the OSS bucket belong to the same region, set the value based on the network type of the cluster.
        -   If the cluster uses a virtual private cloud \(VPC\), select **VPC Endpoint**.
        -   If the cluster uses the classic network, select **Internal Endpoint**. |
    |**Label**|Add labels to the PV.|

7.  Click **Create**.


**Step 2: Create a PVC**

1.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volume Claims**.

2.  In the upper-right corner of the **Persistent Volume Claims** page, click **Create**.

3.  In the Create PVC dialog box, set the following parameters.

    |Parameter|Description|
    |---------|-----------|
    |**PVC Type**|You can select Cloud Disk, NAS, or OSS. In this example, OSS is selected.|
    |**Name**|The name of the persistent volume claim \(PVC\). The name must be unique in the cluster.|
    |**Allocation Mode**|In this example, Existing Volumes is selected. **Note:** If no PV is created, you can set **Allocation Mode** to **Create Volume**, and set other parameters to create a PV. For more information, see [Step 1: Create a PV](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/OSS volumes/Use a statically provisioned OSS volume.md). |
    |**Existing Volumes**|Click **Select PV**. Find the PV that you want to use and click **Select** in the Actions column.|
    |**Capacity**|The capacity claimed by the PVC. **Note:** The capacity claimed by the PVC cannot exceed the capacity of the PV. |

4.  Click **Create**.

    After the PVC is created, you can find the PVC named **csi-oss-pvc** in the list of PVCs, and the PVC is bound to the selected PV.


**Step 3: Create an application**

1.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

2.  In the upper-right corner of the **Deployments** page, click **Create from Image**.

3.  Set the parameters used to create an application.

    This example shows how to set the volume parameters. For more information about other parameters, see [Create a stateless application by using a Deployment](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Create a stateless application by using a Deployment.md).

    You can add local volumes and cloud volumes.

    -   **Add Local Storage**: You can select HostPath, ConfigMap, Secret, or EmptyDir from the PV Type drop-down list. Then, set the Mount Source and Container Path parameters to mount the volume to a container path. For more information, see [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE).
    -   **Add PVC**: You can add cloud volumes.
    In this example, csi-oss-pvc is selected as the Mount Source and mounted to the /tmp path in the container.

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4606191261/p69158.png)

4.  Set other parameters and click **Create**.

    After the application is created, you can use the OSS volume to store application data.


## Use a statically provisioned OSS volume by using kubectl

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

    The following code provides an example of the pv-oss.yaml file:

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
    |name|The name of the PV.|
    |labels|The labels that are added to the PV.|
    |storage|The available storage of the OSS bucket.|
    |accessModes|The access mode of the PV.|
    |persistentVolumeReclaimPolicy|The policy for reclaiming the PV.|
    |driver|The type of driver. This parameter is set to ossplugin.csi.alibabacloud.com. This value indicates that the OSS Container Storage Interface \(CSI\) plug-in provided by Alibaba Cloud is used.|
    |nodePublishSecretRef|Specifies that the AccessKey pair is retrieved from a Secret when an OSS bucket is mounted by using the PV.|
    |volumeHandle|Specifies the name of the PV.|
    |bucket|Specifies the OSS bucket to be mounted.|
    |url|Specifies the endpoint of the OSS bucket. If the OSS bucket and the node to which the OSS bucket is mounted belong to the same region, you can use the internal endpoint of the OSS bucket.|
    |otherOpts|You can enter custom parameters in the format of `-o *** -o ***`.|
    |path|Specifies the path relative to the root directory of the OSS bucket to be mounted. The default value is /. This parameter is supported by csi-plugin 1.14.8.32-c77e277b-aliyun and later.|

    Log on to the [ACK console](https://cs.console.aliyun.com). In the left-side navigation pane, click **Clusters**. On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Details** in the **Actions** column. The details page of the cluster appears. In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volumes**. You can view the created PV on the Persistent Volumes page.

3.  Run the following command to create the PV and PVC:

    ```
    kubectl create -f pvc-oss.yaml
    ```

    The following code provides an example of the pvc-oss.yaml file:

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
    |`name`|The name of the PVC.|
    |`accessModes`|The access mode of the PVC.|
    |`storage`|The capacity claimed by the PVC. The claimed capacity cannot exceed the capacity of the PV.|
    |`alicloud-pvname`|The labels that are used to bind the PVC to the PV. The labels must be the same as those added to the PV.|

    In the left-side navigation pane, choose **Volumes** \> **Persistent Volume Claims**. On the Persistent Volume Claims page, you can view the created PVC.


Method 2: Specify an AccessKey pair when you create a PV and a PVC

The previous section [Method 1: Create a static PV and a PVC by using a Secret](#p_dum_5ej_hg0) describes how to specify an AccessKey pair for the CSI plug-in by using a Secret.

You can also run the following command to directly specify the AccessKey pair in the PV configurations:

```
kubectl create -f pv-accesskey.yaml
```

The following code provides an example of the pv-accesskey.yaml file:

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

In addition to the methods described in [Method 1: Create a static PV and a PVC by using a Secret](#p_dum_5ej_hg0) and [Method 2: Specify an AccessKey pair when you create a PV and a PVC](#p_mfp_yr3_ovq),

you can also run the following command to configure security token-based verification:

```
kubectl create -f pv-sts.yaml
```

The following code provides an example of the pv-sts.yaml file:

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

Deploy an application named oss-static and mount the PVC to the application.

Run the following command to create a file named oss-static.yaml.

```
kubectl create -f oss-static.yaml
```

The following code provides an example of the oss-static.yaml file:

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

-   `livenessProbe`: health check settings. For more information, see [Overview](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/OSS volumes/Overview.md).
-   `mountPath`: the path where the OSS bucket is mounted in the container.
-   `claimName`: the name of the PVC that is mounted to the application.

## Verify that the OSS bucket can be used to persist data

1.  View the pod where the oss-static application is deployed and the files in the OSS bucket.

    1.  Run the following command to query the pod where the oss-static application is deployed:

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

        No output is returned. This indicates that no file exists in the /data path.

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

    1.  Run the following command to query the name of the recreated pod:

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


