---
keyword: [statically provisioned NAS volume, persistent storage, shared storage]
---

# Use a statically provisioned NAS volume

Apsara File Storage NAS \(NAS\) is a distributed file system that supports shared access, elastic scaling, high reliability, and high performance. This topic describes how to use a statically provisioned NAS volume, and how to enable persistent storage and shared storage by using a statically provisioned NAS volume.

-   A Container Service for Kubernetes \(ACK\) cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   A NAS file system is created. For more information, see [Create a NAS file system]().

    If you want to encrypt data in a NAS volume, configure the encryption settings when you create the NAS file system.

-   A mount target is created for the NAS file system. For more information, see [Manage mount targets]().

    The mount target and the cluster node to which you want to mount the NAS file system must belong to the same virtual private cloud \(VPC\).

-   A kubectl client is connected to the cluster. For more information, see [Connect to ACK clusters by using kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md).

## Scenarios

-   Your application requires high disk I/O.
-   You need a storage service that offers higher read and write throughput than Object Storage Service \(OSS\).
-   You want to share files across hosts. For example, you want to use a NAS file system as a file server.

## Precautions

-   To mount an Extreme NAS file system, set the `path` parameter of the NAS volume to a subdirectory of /share. For example, you can specify the /share/path1 subdirectory when you mount an Extreme NAS file system to a pod.
-   If a NAS file system is mounted to multiple pods, the data in the file system is shared by the pods. In this case, the application must be able to synchronize data across these pods when data modifications are made by multiple pods.

    **Note:** You cannot grant permissions to access the / directory \(root directory\) of the NAS file system. The user account and user group to which the directory belongs cannot be modified.

-   If the securityContext.fsgroup parameter is set in the application template, kubelet performs the `chmod` or `chown` operation after the volume is mounted, which increases the time consumption.

## Use a statically provisioned NAS volume in the ACK console

**Step 1: Create a PV**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volumes**.

5.  In the upper-right corner of the **Persistent Volumes** page, click **Create**.

6.  In the **Create PV** dialog box, set the following parameters.

    |Parameter|Description|
    |---------|-----------|
    |**PV Type**|You can select Cloud Disk, NAS, or OSS. In this example, NAS is selected.|
    |**Volume Name**|The name of the persistent volume \(PV\) that you want to create. The name must be unique in the cluster. In this example, pv-nas is used.|
    |**Volume Plug-in**|You can select FlexVolume or CSI. In this example, CSI is selected.|
    |**Capacity**|The capacity of the PV. A NAS file system provides unlimited capacity. This parameter does not limit the storage usage of the NAS file system but defines the capacity of the PV.|
    |**Access Mode**|You can select ReadWriteMany or ReadWriteOnce. Default value: ReadWriteMany.|
    |**Mount Target Domain Name**|You can **select a mount target** or enter a **custom** mount target.|
    |**Show Advanced Options**|    -   **Subdirectory**: the subdirectory of the NAS file system that you want to mount. The subdirectory must start with a forward slash \(/\). After you set this parameter, the PV is mounted to the subdirectory.
        -   If the specified subdirectory does not exist, the system automatically creates the subdirectory in the NAS file system and mounts the subdirectory to the cluster.
        -   If you do not set this parameter, the root directory of the NAS file system is mounted.
        -   If you want to mount an Extreme NAS file system, the subdirectory must be under the /share directory.
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
    |**Name**|The name of the persistent volume claim \(PVC\). The name must be unique in the cluster.|
    |**Allocation Mode**|In this example, Existing Volumes is selected. **Note:** If no PV is created, you can set **Allocation Mode** to **Create Volume**, and set the required parameters to create a PV. For more information, see [Create a PV](#p_93m_jyx_d0a). |
    |**Existing Volumes**|Click **Select PV**. Find the PV that you want to use and click **Select** in the Actions column.|
    |**Capacity**|The capacity claimed by the PVC. **Note:** The capacity claimed by the PVC cannot exceed the capacity of the PV that is bound to the PVC. |

4.  Click **Create**.

    After the PVC is created, you can view the PVC in the PVCs list. The PVC is bound to the corresponding PV.


**Step 3: Create an application**

1.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

2.  In the upper-right corner of the **Deployments** page, click **Create from Image**.

3.  Set the application parameters.

    This example shows how to set the volume parameters. For more information about other parameters, see [Create a stateless application by using a Deployment](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Create a stateless application by using a Deployment.md).

    You can add local volumes and cloud volumes.

    -   **Add Local Storage**: You can select HostPath, ConfigMap, Secret, or EmptyDir from the PV Type drop-down list. Then, set the Mount Source and Container Path parameters to mount the volume to a container path. For more information, see [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE).
    -   **Add PVC**: You can add cloud volumes.
    In this example, a NAS volume is specified as the mount source and mounted to the /tmp path in the container.

    ![Volumes](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/8927051261/p59980.jpg)

4.  Set the other parameters and click **Create**.

    After the application is created, you can use the NAS volume to store application data.


## Use a statically provisioned NAS volume by using kubectl

1.  Run the following command to create a statically provisioned PV:

    ```
    kubectl create -f pv-nas.yaml
    ```

    The following YAML template provides an example on how to create a statically provisioned PV:

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
          server: "2564f4****-ysu87.cn-shenzhen.nas.aliyuncs.com"
          path: "/csi"
      mountOptions:
      - nolock,tcp,noresvport
      - vers=3
    ```

    |Parameter|Description|
    |---------|-----------|
    |name|The name of the PV.|
    |labels|The labels that you want to add to the PV.|
    |storage|The capacity of the NAS volume.|
    |accessModes|The access mode of the PV.|
    |driver|The type of driver. In this example, the parameter is set to `nasplugin.csi.alibabacloud.com`. This indicates that the NAS Container Storage Interface \(CSI\) plug-in provided by Alibaba Cloud is used.|
    |volumeHandle|The unique identifier of the PV. If multiple PVs are used, the identifier of each PV must be unique.|
    |server|The mount target of the NAS file system.|
    |path|The subdirectory of the NAS file system that you want to mount. If you want to mount an Extreme NAS file system, the subdirectory must be under the /share directory.|
    |vers|The version of the Network File System \(NFS\) protocol. We recommend that you use NFSv3. Extreme NAS file systems support only NFSv3.|

2.  Run the following command to create a PVC used for static provisioning:

    When you create a PVC of the NAS type, set the selector parameter to configure how to select a PV and bind it to the PVC.

    ```
    kubectl create -f pvc-nas.yaml
    ```

    The following YAML template provides an example on how to create a PVC used for static provisioning:

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

    |Parameter|Description|
    |---------|-----------|
    |name|The name of the PVC.|
    |accessModes|The access mode of the PVC.|
    |storage|The capacity claimed by the PVC. The claimed capacity cannot exceed the capacity of the PV bound to the PVC.|
    |mathLabels|The labels are used to select the PV that is bound to the PVC.|

3.  Run the following command to create an application named **nas-static** and mount the created PVC to the application:

    ```
    kubectl create -f nas.yaml
    ```

    The following YAML tempalte provides an example of the nas.yaml file that is used to create the **nas-static** application:

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nas-static
      labels:
        app: nginx
    spec:
      replicas: 2
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
              - name: pvc-nas
                mountPath: "/data"
          volumes:
            - name: pvc-nas
              persistentVolumeClaim:
                claimName: pvc-nas
    ```

    |Parameter|Description|
    |---------|-----------|
    |mountPath|The path of the container where the NAS volume is mounted.|
    |claimName|The name of the PVC mounted to the application.|

4.  Run the following command to query the pods that run the application:

    ```
    kubectl get pod
    ```

    Expected output:

    ```
    NAME                          READY   STATUS    RESTARTS   AGE
    nas-static-5b5cdb85f6-n****   1/1     Running   0          32s
    nas-static-c5bb4746c-4****    1/1     Running   0          32s
    ```


## Verify that the NAS file system can be used to persist data

1.  Query the pods that run the application and the files in the mounted NAS file system.

    1.  Run the following command to query the pods that run the application:

        ```
        kubectl get pod 
        ```

        Expected output:

        ```
        NAME                          READY   STATUS    RESTARTS   AGE
        nas-static-5b5cdb85f6-n****   1/1     Running   0          32s
        nas-static-c5bb4746c-4****    1/1     Running   0          32s
        ```

    2.  Run the following command to query files in the /data path of a pod. The pod `nas-static-5b5cdb85f6-n****` is used as an example:

        ```
        kubectl exec nas-static-5b5cdb85f6-n**** ls /data
        ```

        No output is returned. This indicates that no file exists in the /data path.

2.  Run the following command to create a file named nas in the /data path of the pod `nas-static-5b5cdb85f6-n****`:

    ```
    kubectl exec nas-static-5b5cdb85f6-n**** touch /data/nas
    ```

3.  Run the following command to query files in the /data path of the pod `nas-static-5b5cdb85f6-n****`:

    ```
    kubectl exec nas-static-5b5cdb85f6-n**** ls /data
    ```

    Expected output:

    ```
    nas
    ```

4.  Run the following command to delete the pod:

    ```
    kubectl delete pod nas-static-5b5cdb85f6-n****
    ```

5.  Open another command-line interface \(CLI\) and run the following command to view how the pod is deleted and recreated:

    ```
    kubectl get pod -w -l app=nginx
    ```

6.  Verify that the file still exists after the pod is deleted.

    1.  Run the following command to query the name of the recreated pod:

        ```
        kubectl get pod
        ```

        Expected output:

        ```
        NAME                                READY   STATUS    RESTARTS   AGE
        nas-static-5b5cdb85f6-n****   1/1     Running   0          32s
        nas-static-c5bb4746c-4****    1/1     Running   0          32s
        ```

    2.  Run the following command to query files in the /data path of the pod `nas-static-5b5cdb85f6-n****`:

        ```
        kubectl exec nas-static-5b5cdb85f6-n**** ls /data
        ```

        Expected output:

        ```
        nas
        ```

        The nas file still exists in the /data path. This indicates that data is persisted to the NAS file system.


## Verify that data in the NAS file system can be shared across pods

1.  Query the pods that run the application and the files in the mounted NAS file system.

    1.  Run the following command to query the pods that run the application:

        ```
        kubectl get pod 
        ```

        Expected output:

        ```
        NAME                          READY   STATUS    RESTARTS   AGE
        nas-static-5b5cdb85f6-n****   1/1     Running   0          32s
        nas-static-c5bb4746c-4****    1/1     Running   0          32s
        ```

    2.  Run the following command to query files in the /data path of each pod:

        ```
        kubectl exec nas-static-5b5cdb85f6-n**** ls /data
        kubectl exec nas-static-c5bb4746c-4**** ls /data
        ```

2.  Run the following command to create a file named nas in the /data path of a pod:

    ```
     kubectl exec nas-static-5b5cdb85f6-n**** touch /data/nas
    ```

3.  Query files in the /data path of each pod.

    1.  Run the following command to query files in the /data path of the pod `nas-static-5b5cdb85f6-n****`:

        ```
        kubectl exec nas-static-5b5cdb85f6-n**** ls /data
        ```

        Expected output:

        ```
        nas
        ```

    2.  Run the following command to query files in the /data path of the pod `nas-static-c5bb4746c-4****`:

        ```
        kubectl exec nas-static-c5bb4746c-4**** ls /data
        ```

        Expected output:

        ```
        nas
        ```

        When you create a file in the /data path of one pod, you can also find the file in the /data path of the other pod. This indicates that data in the NAS file system is shared by the two pods.


