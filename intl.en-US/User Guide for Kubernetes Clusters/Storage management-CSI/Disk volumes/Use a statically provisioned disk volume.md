---
keyword: [statically provisioned disk volumes, CSI plug-in, K8s]
---

# Use a statically provisioned disk volume

Alibaba Cloud disks are block-level storage resources for Elastic Compute Service \(ECS\). Alibaba Cloud disks provide low latency, high performance, high durability, and high reliability. Container Service for Kubernetes \(ACK\) allows you to use the CSI plug-in to statically and dynamically provision disk volumes. This topic describes how to mount a statically provisioned disk volume by using the CSI plug-in.

-   An ACK cluster is created and the CSI plug-in is deployed in the cluster. For more information, see [Install the CSI plug-in](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Install the CSI plug-in.md).
-   A pay-as-you-go disk is created. The disk ID is `d-wz92s6d95go6ki9x****`. For more information, see [Create a disk](/intl.en-US/Block Storage/Cloud disks/Create a cloud disk/Create a disk.md).

    **Note:** Your disk must meet the following requirements:

    -   A basic disk must have a minimum capacity of 5 GiB.
    -   An ultra disk must have a minimum capacity of 20 GiB.
    -   A standard SSD must have a minimum capacity of 20 GiB.
    -   An enhanced SSD \(ESSD\) must have a minimum capacity of 20 GiB.

## Mount a statically provisioned disk volume by using kubectl

Before you use kubectl to mount a statically provisioned disk volume, make sure that you can connect to the cluster by using kubectl. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).

**Step 1: Create a static PV and PVC.**

1.  Use the following template to create a static persistent volume \(PV\) and persistent volume claim \(PVC\):

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
    ---
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
        volumeHandle: d-wz92s6d95go6ki9x****
      nodeAffinity:
        required:
          nodeSelectorTerms:
          - matchExpressions:
            - key: topology.diskplugin.csi.alibabacloud.com/zone
              operator: In
              values:
              - cn-shenzhen-a
    ```

    |Parameter|Description|
    |---------|-----------|
    |driver|The type of driver. This parameter is set to `diskplugin.csi.alibabacloud.com`. The value indicates that the Alibaba Cloud CSI plug-in is used. |
    |volumeHandle|The ID of the disk.|
    |nodeAffinity|Information about the zone to which the PV and PVC belong. You can set this parameter to specify the zone to which the pod of the PV and PVC is scheduled. |


**Step 2: Create an application.**

1.  Create a file named nginx-disk-dept.yaml and copy the following content to the file:

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

2.  Run the following command to create an application that is mounted with a statically provisioned disk volume:

    ```
    kubectl apply -f nginx-disk-dept.yaml
    ```


## Use a statically provisioned disk volume in the ACK console

**Step 1: Create a PV.**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volumes**.

5.  In the upper-right corner of the **Persistent Volumes** page, click **Create**.

6.  In the **Create PV** dialog box, set the following parameters.

    |Parameter|Description|
    |---------|-----------|
    |**PV Type**|You can select Cloud Disk, NAS, or OSS. In this example, Cloud Disk is selected.|
    |**Volume Plug-in**|You can select FlexVolume or CSI. In this example, CSI is selected.|
    |**Access Mode**|By default, this parameter is set to ReadWriteOnce.|
    |**Disk ID**|Select a disk that is in the same region and zone as your cluster.|
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
    |**Name**|The name of the PVC. The name must be unique in the namespace.|
    |**Allocation Mode**|In this example, Existing Volumes is selected. **Note:** If no PV is created, you can set **Allocation Mode** to **Create Volume** and configure a PV. For more information, see [Create a PV](#p_z04_dh8_66f). |
    |**Existing Volumes**|Click **Select PV**. In the dialog box that appears, find the PV that you want to use and click **Select** in the Actions column.|
    |**Capacity**|The capacity of the PV. **Note:** The capacity of the PVC cannot exceed the capacity of the disk. |
    |**Access Mode**|By default, this parameter is set to ReadWriteOnce.|

4.  Click **Create**.

    After the PVC is created, you can find the PVC in the list of PVCs. The PVC is associated with the specified PV.


**Step 3: Create an application**

1.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

2.  In the upper-right corner of the **Deployments** page, click **Create from Image**.

3.  Set the parameters that are required for creating an application.

    This example shows how to set the volume parameters. For more information about other parameters, see [Use a Deployment to create a stateless application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Use a Deployment to create a stateless application.md).

    You can add local volumes and cloud volumes.

    -   **Add Local Storage**: You can select HostPath, ConfigMap, Secret, or EmptyDir from the PV Type drop-down list. Then, set the Mount Source and Container Path parameters to mount the volume to a container path. For more information, see [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE).
    -   **Add PVC**: You can add cloud volumes.
    Mount the disk volume that is created in this example to the /tmp path of the container. After the disk volume is mounted, container data that is generated in the /tmp path is saved to the disk volume.

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9995051261/p68586.png)

4.  Set other parameters and click **Create**.

    After the application is created, you can use the disk volume to store application data.


