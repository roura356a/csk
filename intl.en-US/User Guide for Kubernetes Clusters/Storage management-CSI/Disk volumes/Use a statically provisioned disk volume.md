---
keyword: [statically provisioned disk volume, the CSI plug-in, K8s, persistent storage]
---

# Use a statically provisioned disk volume

Alibaba Cloud disks are block-level storage resources for Elastic Compute Service \(ECS\). Alibaba Cloud disks provide low latency, high performance, high durability, and high reliability. Container Service for Kubernetes \(ACK\) allows you to use the Container Storage Interface \(CSI\) plug-in to statically and dynamically provision disk volumes. This topic describes how to use the CSI plug-in to mount a statically provisioned disk volume and how to enable persistent storage by using a statically provisioned disk volume.

-   An ACK cluster is created and the CSI plug-in is deployed in the cluster. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   A pay-as-you-go disk is created. The disk ID is `d-wz92s6d95go6ki9x****`. For more information, see [Create a disk](/intl.en-US/Block Storage/Cloud disks/Create a cloud disk/Create a disk.md).

    **Note:** Your disk must meet the following requirements:

    -   An ultra disk must have a minimum capacity of 20 GiB.
    -   A standard SSD must have a minimum capacity of 20 GiB.
    -   An enhanced SSD \(ESSD\) must have a minimum capacity of 20 GiB.
-   [Step 2: Select a type of cluster credentials](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md).

Scenarios:

-   You want to create applications that require high disk I/O and do not require data sharing. The applications can use storage services such as MySQL and Redis.
-   You want to write logs at high speeds.
-   You want to persist data in a way that is independent of the pod lifecycle.

To mount a disk as a statically provisioned volume, make sure that you have purchased a disk.

Manually create a persistent volume \(PV\) and a persistent volume claim \(PVC\) that are used to statically provision a disk.

## Limits

-   Alibaba Cloud disks cannot be shared. A disk can be mounted only to one pod.
-   A disk can be mounted only to a node that is deployed in the same zone as the disk.

## Use a statically provisioned disk volume in the ACK console

**Step 1: Create a PV.**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volumes**.

5.  In the upper-right corner of the **Persistent Volumes** page, click **Create**.

6.  In the **Create PV** dialog box, set the following parameters.

    |Parameter|Description|
    |---------|-----------|
    |**PV Type**|You can select Cloud Disk, NAS, or OSS. In this example, Cloud Disk is selected.|
    |**Volume Plug-in**|You can select FlexVolume or CSI. In this example, CSI is selected.|
    |**Access Mode**|By default, this parameter is set to ReadWriteOnce.|
    |**Disk ID**|Select a mountable disk that is deployed in the same region and zone as your cluster.|
    |**File System Type**|Select the file system of the disk. Valid values: **ext4**, **ext3**, **xfs**, and **vfat**. Default value: **ext4**.|
    |**Label**|Add labels to the PV.|

7.  Click **Create**.


**Step 2: Create a PVC.**

1.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volume Claims**.

2.  In the upper-right corner of the **Persistent Volume Claims** page, click **Create**.

3.  In the **Create PVC** dialog box, set the following parameters:

    |Parameter|Description|
    |---------|-----------|
    |**PVC Type**|You can select Cloud Disk, NAS, or OSS. In this example, Cloud Disk is selected.|
    |**Volume Name**|The name of the PVC. The name must be unique in the namespace.|
    |**Allocation Mode**|In this example, Existing Volumes is selected. **Note:** If no PV is created, you can set **Allocation Mode** to **Create Volume**, and set other parameters to create a PV. For more information, see [Create a PV](#p_ni4_k9a_sov). |
    |**Existing Volumes**|Click **Select PV**. In the dialog box that appears, find the PV that you want to use and click **Select** in the Actions column.|
    |**Capacity**|The capacity of the selected PV. **Note:** The capacity of a PV cannot be greater than the disk that is associated to the PV. |
    |**Access Mode**|By default, this parameter is set to ReadWriteOnce.|

4.  Click **Create**.

    After the PVC is created, you can find the PVC in the list of PVCs. The PVC is bound to the specified PV.


**Step 3: Create an application**

1.  In the left-side navigation pane of the details page, choose **Workloads** \> **StatefulSets**.

2.  In the upper-right corner of the **StatefulSets** page, click **Create from Image**.

3.  Set the application parameters.

    This example shows how to set the volume parameters. For more information about other parameters, see [Use a StatefulSet to create a stateful application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Use a StatefulSet to create a stateful application.md).

    You can add local volumes and cloud volumes.

    -   **Add Local Storage**: You can select HostPath, ConfigMap, Secret, or EmptyDir from the PV Type drop-down list. Then, set the Mount Source and Container Path parameters to mount the volume to a container path. For more information, see [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE).
    -   **Add PVC**: You can add cloud volumes.
    Mount the disk volume that is created in this example to the /tmp path of the container. After the disk volume is mounted, the container data that is generated in the /tmp path is stored in the disk volume.

    ![Data storage](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/8733023261/p68586.png)

4.  Set other parameters and click **Create**.

    After the application is created, you can use the disk volume to store application data.


## Mount a statically provisioned disk volume by using kubectl

**Step 1: Create a PV**

1.  Use the following template to create a pv-static.yaml file:

    ```
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: csi-pv
      labels:
        alicloud-pvname: static-disk-pv
    spec:
      capacity:
        storage: 25Gi
      accessModes:
        - ReadWriteOnce
      persistentVolumeReclaimPolicy: Retain
      csi:
        driver: diskplugin.csi.alibabacloud.com
        volumeHandle: "<your-disk-id>"
      nodeAffinity:
        required:
          nodeSelectorTerms:
          - matchExpressions:
            - key: topology.diskplugin.csi.alibabacloud.com/zone
              operator: In
              values:
              - "<your-node-zone-id>"
    ```

    |Parameter|Description|
    |---------|-----------|
    |`name`|The name of the PV.|
    |`labels`|The labels that you want to add to the PV.|
    |`storage`|The available storage of the disk.|
    |`accessModes`|The access mode of the PV.|
    |`persistentVolumeReclaimPolicy`|The policy for reclaiming the PV.|
    |`driver`|The type of the volume driver that you want to use. This parameter is set to `diskplugin.csi.alibabacloud.com`. This value indicates that the Alibaba Cloud CSI plug-in is used.|
    |`volumeHandle`|The ID of the disk.|
    |`nodeAffinity`|Information about the zone to which the PV and PVC belong. You can set this parameter to specify the zone to which the pod that uses the PV and PVC is scheduled. |

2.  Run the following command to create a PV:

    ```
    kubectl create -f pv-static.yaml
    ```

    Verify the result.

    1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).
    2.  In the left-side navigation pane, click **Clusters**.
    3.  On the Clusters page, find the cluster that you want to manage and click the name or click **Details** in the **Actions** column.
    4.  In the left-side navigation pane of the cluster details page, choose **Volumes** \> **Persistent Volumes**.
    You can view the created PV on the Persistent Volumes page.


**Step 2: Create a PVC**

1.  Use the following template to create a pvc-static.yaml file:

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: csi-pvc
    spec:
      accessModes:
      - ReadWriteOnce
      resources:
        requests:
          storage: 25Gi
      selector:
        matchLabels:
          alicloud-pvname: static-disk-pv
    ```

    |Parameter|Description|
    |---------|-----------|
    |`name`|The name of the PVC.|
    |`accessModes`|The access mode of the PVC.|
    |`storage`|The capacity claimed by the PVC. The claimed capacity cannot exceed the capacity of the PV that is bound to the PVC.|
    |`matchLabels`|The labels that are used to select a PV and bind it to the PVC. The labels must be the same as those of the PV.|

2.  Run the following command to create a PVC:

    ```
    kubectl create -f pvc-static.yaml
    ```

    Verify the result.

    In the left-side navigation pane of the cluster details page, choose **Volumes** \> **Persistent Volume Claims**. On the Persistent Volume Claims page, you can view the created PVC.


**Step 3: Create an application**

Deploy an application named nginx-static and mount the PVC to the application. In this example, an NGINX application is created.

1.  Use the following code to create an nginx-static.yaml file:

    ```
    apiVersion: apps/v1
    kind: StatefulSet
    metadata:
      name: web
    spec:
      selector:
        matchLabels:
          app: nginx
      serviceName: "nginx"
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
            - name: pvc-disk
              mountPath: /data
          volumes:
            - name: pvc-disk
              persistentVolumeClaim:
                claimName: csi-pvc
    ```

    -   `mountPath`: the path where you want to mount the disk in the container.
    -   `claimName`: the name of the PVC that you want to mount to the application.
2.  Run the following command to create an application and mount the statically provisioned PV by using the PVC:

    ```
    kubectl apply -f nginx-static.yaml
    ```

    Verify the result.

    In the left-side navigation pane of the cluster details page, choose **Workloads** \> **StatefulSets**. You can view the created application on the StatefulSets page.


## Verify that the statically provisioned disk can be used to persist data

1.  View the pod where the nginx-static application is deployed and files in the disk.

    1.  Run the following command to query the names of pods that run the nginx-static application:

        ```
        kubectl get pod | grep nginx-static
        ```

        Expected output:

        ```
        nginx-**** 1/1 Running 0 32s
        ```

    2.  Run the following command to check whether a new disk is mounted to the /data path:

        ```
        kubectl exec nginx-**** df | grep data
        ```

        Expected output:

        ```
        /dev/vdf 20511312 45080 20449848 1% /data
        ```

    3.  Run the following command to query files in the /data path:

        ```
        kubectl exec nginx-**** ls /data
        ```

        Expected output:

        ```
        lost+found
        ```

2.  Run the following command to create a file named static in the /data path:

    ```
    kubectl exec nginx-**** touch /data/static
    ```

3.  Run the following command to query files in the /data path:

    ```
     kubectl exec nginx-**** ls /data
    ```

    Expected output:

    ```
    lost+found
    static
    ```

4.  Run the following command to delete the `nginx-****` pod:

    ```
    kubectl delete pod nginx-****
    ```

    Expected output:

    ```
    pod "nginx-****" deleted
    ```

5.  Verify that the file still exists in the disk after the pod is deleted.

    1.  Run the following command to query the pod that is recreated.

        ```
        kubectl get pod
        ```

        Expected output:

        ```
        NAME       READY   STATUS    RESTARTS    AGE 
        nginx-**** 1/1     Running   0           14s
        ```

    2.  Run the following command to query files in the /data path:

        ```
        kubectl exec nginx-**** ls /data
        ```

        Expected output:

        ```
        lost+found
        static
        ```

        If the static file still exists in the disk, it indicates that data is persisted to the statically provisioned disk.


