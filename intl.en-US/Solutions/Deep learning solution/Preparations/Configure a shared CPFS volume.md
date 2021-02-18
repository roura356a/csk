---
keyword: [shared CPFS volumes, Arena, distributed storage, configure PVs and PVCs]
---

# Configure a shared CPFS volume

Cloud Paralleled File System \(CPFS\) is a type of parallel file system provided by Alibaba Cloud. CPFS stores data across multiple data nodes in a cluster and allows data to be simultaneously accessed by multiple clients. Therefore, CPFS can provide data storage services with high input/output operations per second \(IOPS\), high throughput, and low latency for large, high-performance computing clusters. This topic describes how to configure a shared CPFS volume.

Data scientists may want to persist their data or retrieve the same copy of training data. To meet this requirement, we recommend that you configure a shared NAS volume and mount it to the runtime where you use Arena to submit jobs. This prevents data loss because the code and data that are stored in the shared NAS volume are not deleted with containers.

We recommend that you allow the developers in a team to share the same storage pool. If you declare a shared NAS volume and specify the path that is used to mount the volume, the shared NAS volume is automatically mounted to the specified path. After the shared NAS volume is mounted, you can consume the data and code that are stored in the volume by specifying the --data parameter each time you submit a job.

In Kubernetes, storage volumes are declared by using persistent volumes \(PVs\) and persistent volume claims \(PVCs\). As the administrator of a Kubernetes cluster, you must create a PVC for each data scientist in your team. For example, User A and User B can mount their PVs to the same NAS volume or Cloud Paralleled File System \(CPFS\). However, the PVs must be mounted to different subdirectories to isolate the runtimes of User A and User B.

## Step 1: Create a CPFS file system

For more information about how to create a CPFS file system, see [Create a file system]().

**Note:**

When you create a CPFS file system, set the region, Virtual Private Cloud \(VPC\) network and VSwitches to those of the target cluster of Alibaba Cloud Container Service for Kubernetes \(ACK\).

## Step 2: Add a mount target

1.  Log on to the [NAS console](https://nasnext.console.aliyun.com/).

2.  In the left-side navigation pane, choose **File System** \> **File System List**.

3.  Find the newly created CPFS file system and click **Management** in the **Operations** column.

    On the **Basic Information** tab, you can view the ID and mount target of the newly created file system.

    ![CPFS](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5976258951/p134706.png)


## Step 3: Create a PV and a PVC for the target ACK cluster

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Applications** \> **Deployments**.

3.  In the upper-right corner of the Deployments page, click **Create from Template**.

    Select the target cluster from the Cluster drop-down list, select the target namespace from the Namespace drop-down list, and select **** from the Sample Template drop-down list.

    Copy the following content to the template:

    ```
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: pv-cpfs
      labels:
        alicloud-pvname: pv-cpfs
    spec:
      capacity:
        storage: 500Gi
      accessModes:
        - ReadWriteMany
      flexVolume:
        driver: "alicloud/cpfs"
        options:
          server: "cpfs-xxxxx.cn-huhehaote.cpfs.nas.aliyuncs.com@tcp:/08fba53c"
          fileSystem: "cpfs-08fba53c"
          subPath: "tf_data"
    
    ---
    kind: PersistentVolumeClaim
    apiVersion: v1
    metadata:
      name: pv-cpfs
    spec:
      accessModes:
        - ReadWriteMany
      resources:
        requests:
          storage: 500Gi
      selector:
        matchLabels:
          alicloud-pvname: pv-cpfs
    ```

    **Note:**

    Set the following parameters when you configure the persistent volume \(PV\) and persistent volume claim \(PVC\):

    -   server: Enter the mount target of the CPFS file system that is obtained in Step 2.
    -   fileSystem: Enter the ID of the CPFS file system.
    -   subPath: Enter tf\_data.
4.  Click **Create**.

    After the PV and PVC are created, you can find the PV in the **Persistent Volumes** list and the PVC in the **Persistent Volume Claims** list.


