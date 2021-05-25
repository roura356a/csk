# Mount a NAS file system as a statically provisioned volume

Apsara File Storage NAS \(NAS\) volumes use distributed file systems. NAS volumes can be shared and are scalable, high-performance, and highly reliable. This topic describes how to mount a NAS file system as a statically provisioned volume.

-   A NAS file system is created. For more information, see [Manage file systems]().
-   A mount target is created for the NAS file system. For more information, see [Manage mount targets]().

    The mount target and the cluster node to which the NAS file system is mounted must be deployed in the same virtual private cloud \(VPC\).


## Mount a NAS file system as a statically provisioned volume by using kubectl

Before you use kubectl to mount a NAS file system as a statically provisioned volume, make sure that you can connect to the cluster by using kubectl. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).

1.  Create a static PV.

    The following YAML template is an example on how to create a static persistent volume \(PV\):

    ```
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: pv-nas
      labels:
        alicloud-pvname: pv-nas
    spec:
      capacity:
        storage: 5Gi
      accessModes:
        - ReadWriteMany
      csi:
        driver: nasplugin.csi.alibabacloud.com
        volumeHandle: pv-nas
        volumeAttributes:
          server: "2564f49129-ysu87.cn-shenzhen.nas.aliyuncs.com"
          path: "/csi"
      mountOptions:
      - nolock,tcp,noresvport
      - vers=3
    ```

    **Note:**

    -   `driver`: the type of driver. In this example, the parameter is set to `nasplugin.csi.alibabacloud.com`. This indicates that the Alibaba Cloud NAS CSI plug-in is used.
    -   `volumeHandle`: the name of the PV. If multiple PVs are used, the name of each PV must be unique.
    -   `server`: the address of the mount target.
    -   `path`: the subdirectory of the NAS file system that is mounted. If you mount an Extreme NAS file system, the path must start with /share.
    -   `vers`: the version of the NFS protocol. We recommend that you use NFSv3. Extreme NAS file systems support only NFSv3.
2.  Create a static PVC.

    The following YAML template is an example on how to create a static persistent volume claim \(PVC\):

    ```
    kind: PersistentVolumeClaim
    apiVersion: v1
    metadata:
      name: pvc-nas
    spec:
      accessModes:
        - ReadWriteMany
      resources:
        requests:
          storage: 5Gi
      selector:
        matchLabels:
          alicloud-pvname: pv-nas
    ```

3.  Create an application.

    The following YAML template is an example on how to create an application:

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx-nas
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
              - name: pv-nas
                mountPath: "/data"
          volumes:
            - name: pv-nas
              persistentVolumeClaim:
                claimName: pvc-nas
    ```


## Mount a NAS file system as a statically provisioned volume in the ACK console

**Step 1: Create a PV**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volumes**.

5.  In the upper-right corner of the **Persistent Volumes** page, click **Create**.

6.  In the **Create PV** dialog box, set the following parameters.

    |Parameter|Description|
    |---------|-----------|
    |**PV Type**|You can select Cloud Disk, NAS, or Object Storage Service \(OSS\). In this example, NAS is selected.|
    |**Volume Name**|The name of the PV that you want to create. The name must be unique in the namespace. In this example, pv-nas is used.|
    |**Volume Plug-in**|You can select Flexvolume or CSI. In this example, CSI is selected.|
    |**Capacity**|The capacity of the PV. The NAS file system provides unlimited capacity. This parameter does not limit the usage of the NAS file system but defines the capacity of the PV.|
    |**Access Mode**|You can select ReadWriteMany or ReadWriteOnce. Default value: ReadWriteMany.|
    |**Mount Target Domain Name**|You can **Select Mount Target** or enter a **Custom** mount target.|
    |**Show Advanced Options**|    -   **Subdirectory**: the subdirectory in the NAS file system. The subdirectory must start with a forward slash \(/\). After you set this parameter, the PV is mounted to the subdirectory.
        -   If the specified subdirectory does not exist, the system automatically creates the subdirectory in the NAS file system.
        -   If you do not set this parameter, the PV is mounted to the root directory of the NAS file system.
        -   To specify a subdirectory in an Extreme NAS file system, the subdirectory must start with /share.
    -   **Version**: the version of the PV. |
    |**Label**|Add labels to the PV.|

7.  Click **Create**.


**Step 2: Create a PVC**

1.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volume Claims**.

2.  In the upper-right corner of the **Persistent Volume Claims** page, click **Create**.

3.  In the Create PVC dialog box, set the following parameters.

    |Parameter|Description|
    |---------|-----------|
    |**PVC Type**|You can select Cloud Disk, NAS, or OSS. In this example, NAS is selected.|
    |**Name**|The name of the PVC. The name must be unique in the namespace.|
    |**Allocation Mode**|In this example, Existing Volumes is selected. **Note:** If no PV is created, you can set **Allocation Mode** to **Create Volume**, and set the parameters to create a PV. For more information, see [Step 1: Create a PV](#p_7lp_wfy_9tv). |
    |**Existing Volumes**|Click **Select PV**. Find the PV that you want to use and click **Select** in the Actions column.|
    |**Capacity**|The capacity of the PVC. **Note:** The capacity of the PVC cannot exceed the capacity of the PV to be associated with this PVC. |

4.  Click **Create**.

    After the PVC is created, you can view the PVC in the list of Persistent Volume Claims. The PVC is associated with the corresponding PV.


**Step 3: Create an application**

1.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

2.  In the upper-right corner of the **Deployments** page, click **Create from Image**.

3.  Set the parameters that are required for creating an application.

    This example shows how to set the volume parameters. For more information about other parameters, see [Use a Deployment to create a stateless application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Use a Deployment to create a stateless application.md).

    You can add local volumes and cloud volumes.

    -   **Add Local Storage**: You can select HostPath, ConfigMap, Secret, or EmptyDir from the PV Type drop-down list. Then, set the Mount Source and Container Path parameters to mount the volume to a container path. For more information, see [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE).
    -   **Add PVC**: You can add cloud volumes.
    In this example, csi-nas-pvc is specified as the Mount Source and mounted to the /tmp path in the container.

    ![Volumes](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8927051261/p59980.jpg)

4.  Set other parameters and click **Create**.

    After you create the application, you can use the NAS volume to store application data.


