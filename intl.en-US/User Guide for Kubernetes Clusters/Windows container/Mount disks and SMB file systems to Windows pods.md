---
keyword: [Windows node, SMB, storage resource, disk]
---

# Mount disks and SMB file systems to Windows pods

Container Service for Kubernetes \(ACK\) allows you to mount dynamically provisioned disk volumes and statically provisioned Server Message Block \(SMB\) volumes to Windows pods. This topic describes how to create and mount disk volumes or SMB volumes to pods on Windows nodes.

-   You have the permissions to use FlexVolume.

    If you do not have the permissions to use FlexVolume, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) and apply to be added to a whitelist.

-   An ACK cluster is created and FlexVolume is selected as the volume plug-in of the cluster. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   A Windows node pool is created. For more information, see [Create a Windows node pool](/intl.en-US/User Guide for Kubernetes Clusters/Windows container/Create a Windows node pool.md).
-   A kubectl client is connected to the cluster. For more information, see [Connect to ACK clusters by using kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md).

## Create and mount disk volumes to Windows pods

Alibaba Cloud disks are block storage resources for Elastic Compute Service \(ECS\) instances. Alibaba Cloud disks provide low latency, high performance, high durability, and high reliability.

You can perform the following steps to create and mount disk volumes to Windows pods:

**Step 1: Create a StorageClass**

alicloud-disk-controller checks whether a StorageClass is intended for the Windows file system or the Linux file system based on the value of fstype in the StorageClass.

-   If the value of fstype is set to ext3, ext4, or xfs, the StorageClass is intended for the Linux file system.
-   If the value of fstype is set to ntfs, the StorageClass is intended for the Windows file system.

By default, if FlexVolume is selected as the volume plug-in, the system defines StorageClasses that are intended for the Linux file system when you create an ACK cluster. You must manually define StorageClasses intended for the Windows file system.

1.  Create a file named storageclass.yaml based on the following content to define StorageClasses for the Windows file system:



    The following table describes the configurations of the StorageClasses.

    |YAML example|Description|
    |------------|-----------|
    |    ```
  kind: StorageClass
  apiVersion: storage.k8s.io/v1
  metadata:
    name: alicloud-disk-ssd-windows
  provisioner: alicloud/disk
  parameters:
    type: cloud_ssd
    fstype: ntfs
+ allowVolumeExpansion: true # useless
    ```

|Volume expansion is not supported in Windows. For example, the `allowVolumeExpansion: true` configuration of the StorageClass does not take effect.|
    |    ```
  kind: StorageClass
  apiVersion: storage.k8s.io/v1
  metadata:
    name: alicloud-disk-ssd-windows
  provisioner: alicloud/disk
  parameters:
    type: cloud_ssd
    fstype: ntfs
+ reclaimPolicy: Retain # only unmount
    ```

|You can configure the `reclaimPolicy: Retain` reclaim policy to reduce the chances of accidental data loss by user errors.|
    |    ```
  kind: StorageClass
  apiVersion: storage.k8s.io/v1
  metadata:
    name: alicloud-disk-ssd-windows
  provisioner: alicloud/disk
  parameters:
    type: cloud_ssd
    fstype: ntfs
+   regionId: cn-hangzhou
+   zoneId: cn-hangzhou-b
    ```

|If no region ID or zone ID is specified, the system may fail to mount the disk. You can set the `regionId` and `zoneId` parameters to prevent mounting errors.|

2.  Run the following command to create a StorageClass in the Windows environment:

    ```
    kubectl create -f storageclass.yaml
    ```


**Step 2: Install FlexVolume on Windows nodes**

1.  Use the following YAML template to create a flexvolume-windows.yaml file.

2.  Run the following command to install FlexVolume on all of the Windows worker nodes:

    ```
    kubectl create -f flexvolume-windows.yaml
    ```

3.  After the DaemonSets are deployed, run the following command on a Windows node to query the status of the FlexVolume plug-in alicloud-disk-controller:

    ```
    ls C:\usr\libexec\kubernetes\kubelet-plugins\volume\exec\alicloud~disk.exe\
    ```

    Expected output:

    ```
    Directory: C:\usr\libexec\kubernetes\kubelet-plugins\volume\exec\alicloud~disk.exe
    
    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    -a----        8/12/2021   2:45 PM        5636096 disk.exe
    ```


**Step 3: Create a disk volume and mount it to a Windows pod**

A StatefulSet is used as an example. You can specify the required StorageClass in the volumeClaimTemplates field. Then, the FlexVolume plug-in on the Windows worker node automatically performs the following operations: verifies your credentials, creates a disk, attaches the disk to an ECS instance, and then creates and mounts a disk volume to a Windows pod.

After the StatefulSet is deployed, run the following command to query the persistent volume \(PV\):

```
kubectl get pv
```

Expected output:

```
NAME                     STATUS   VOLUME                   CAPACITY   ACCESS MODES   STORAGECLASS                AGE
ssd-web-windows-disk-0   Bound    d-wz9dihqdkusysc56****   50Gi       RWO            alicloud-disk-ssd-windows   5s
```

**Step 4: Verify that the disk volume can be used to persist data**

PVs are used to persist data. The access log of a pod stored in a PV is retained after the pod is deleted.

1.  Run the following command to access the deployed Window application **web-windows-disk**:

    ```
    for i in {1..2}; do; curl $(kubectl get svc web-windows-disk --template '{{ (index .status.loadBalancer.ingress 0).ip }}'); done
    ```

2.  Run the following command to print the log of the **web-windows-disk-0** container:

    ```
    kubectl logs web-windows-disk-0
    ```

    Expected output:

    ```
    openresty started...
    x.x.x.x - - [14/Aug/2021:18:28:28 +0800] "GET / HTTP/1.1" 200 674 "-" "curl/7.64.1"
    x.x.x.x - - [14/Aug/2021:18:28:32 +0800] "GET / HTTP/1.1" 200 674 "-" "curl/7.64.1"
    ```

3.  Run the following command to delete the **web-windows-disk-0** container:

    ```
    kubectl delete pod web-windows-disk-0
    ```

4.  Run the following command to access the redeployed Windows application **web-windows-disk**:

    ```
    for i in {1..3}; do; curl $(kubectl get svc web-windows-disk --template '{{ (index .status.loadBalancer.ingress 0).ip }}'); done
    ```

5.  Run the following command to print the log of the **web-windows-disk-0** container:

    ```
    kubectl logs web-windows-disk-0
    ```

    Expected output:

    ```
    openresty started...
    x.x.x.x - - [14/Aug/2021:18:28:28 +0800] "GET / HTTP/1.1" 200 674 "-" "curl/7.64.1"
    x.x.x.x - - [14/Aug/2021:18:28:32 +0800] "GET / HTTP/1.1" 200 674 "-" "curl/7.64.1"
    openresty started...
    x.x.x.x - - [14/Aug/2021:18:56:40 +0800] "GET / HTTP/1.1" 200 674 "-" "curl/7.64.1"
    x.x.x.x - - [14/Aug/2021:18:56:40 +0800] "GET / HTTP/1.1" 200 674 "-" "curl/7.64.1"
    x.x.x.x - - [14/Aug/2021:18:56:41 +0800] "GET / HTTP/1.1" 200 674 "-" "curl/7.64.1"
    ```

    The log data of the redeployed **web-windows-disk-0** container indicates that the log file is retained after the container for the **web-windows-disk** application is deleted.


## Create and mount SMB volumes to Windows pods

Apsara File Storage NAS \(NAS\) is a distributed file system that is provided for ECS instances. NAS uses Portable Operating System Interface of UNIX \(POSIX\)-based APIs and is compatible with native operating systems. NAS provides shared access, ensures data consistency, and implements mutual exclusion by using locks.

SMB is a protocol that can be used by NAS and works well in Windows. Different from alicloud-disk-controller, the SMB FlexVolume plug-in is not reliant on Linux worker nodes or Kubernetes controllers.

If Kubernetes controllers are not used, the SMB FlexVolume plug-in can only statically provision PVs and cannot use StorageClasses to dynamically provision PVs.

**Step 1: Install FlexVolume on Windows nodes**

Install FlexVolume on all of the Windows worker nodes. For more information, see Step 2: Install FlexVolume on Windows nodes of [Create and mount disk volumes to Windows pods](#section_k1m_mb2_90c).

**Step 2: Create an SMB PV and mount the PV to a Windows pod**

1.  Create a PV.

    Use the following YAML template to create a PV and a persistent volume claim \(PVC\), and apply for a NAS file system of 10 GiB in size.

    The following table describes some of the parameters in the template.

    |Parameter|Description|
    |---------|-----------|
    |driver|The type of driver that is used to mount the PV. The value must be set to `alicloud/smb.exe`.|
    |server|The mount address of the mount target that is added for the NAS file system. The region of the mount address must be the same as that of the virtual private cloud \(VPC\) where the Windows nodes are deployed.|
    |path|The path where the NAS file system is mounted in the container. The path must start with /myshare/. This means that all the subdirectories are created under the parent directory /myshare/. Example: /myshare/web-windows-logs.|
    |user|The username used to log on to the ECS instance. We recommend that you use **workgroup/administrator**.|
    |password|The password used to log on to the ECS instance.|

2.  Use the following YAML template to create a Service and a Deployment, and mount the created PV.

3.  After the Deployment is deployed, run the following commands to query the Deployment of the application and the PV.

    -   Run the following command to query the Deployment:

        ```
        kubectl get pod
        ```

        Expected output:

        ```
        NAME                               READY   STATUS              RESTARTS   AGE
        web-windows-smb-584676df65-t4mmh   1/1     Running             0          26d
        ```

    -   Run the following command to query the PV:

        ```
        kubectl get pv
        ```

        Expected output:

        ```
        NAME         CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM                       STORAGECLASS      REASON   AGE
        pv-smb-10g   10Gi       ReadWriteMany  Retain           Bound       default/web-windows-smb                                10d
        ```


**Step 4: Verify that the NAS file system can be used to persist data**

PVs are used to persist data. The access log of a pod stored in a PV is retained after the pod is deleted.

1.  Run the following command to access the deployed Windows application **web-windows-smb**:

    ```
    for i in {1..2}; do; curl $(kubectl get svc web-windows-smb --template '{{ (index .status.loadBalancer.ingress 0).ip }}'); done
    ```

2.  Run the following command to print the log of the **web-windows-smb-584676df65-t4mmh** container:

    ```
    kubectl logs web-windows-smb-584676df65-t4mmh
    ```

    Expected output:

    ```
    openresty started...
    x.x.x.x - - [14/Aug/2021:18:28:28 +0800] "GET / HTTP/1.1" 200 674 "-" "curl/7.64.1"
    x.x.x.x - - [14/Aug/2021:18:28:32 +0800] "GET / HTTP/1.1" 200 674 "-" "curl/7.64.1"
    ```

3.  Run the following command to delete the **web-windows-smb-584676df65-t4mmh** container:

    ```
    kubectl delete pod web-windows-smb-584676df65-t4mmh
    ```

4.  Run the following command to access the redeployed Windows application **web-windows-smb**:

    ```
    for i in {1..3}; do; curl $(kubectl get svc web-windows-smb --template '{{ (index .status.loadBalancer.ingress 0).ip }}'); done
    ```

5.  Run the following command to query the pod:

    ```
    kubectl get pod
    ```

    Expected output:

    ```
    NAME                               READY   STATUS              RESTARTS   AGE
    web-windows-smb-584676df65-87bpz   1/1     Running             0          26d
    ```

6.  Run the following command to print the log of the **web-windows-smb-584676df65-87bpz** container:

    ```
    kubectl logs web-windows-smb-584676df65-87bpz
    ```

    Expected output:

    ```
    openresty started...
    x.x.x.x - - [14/Aug/2021:18:28:28 +0800] "GET / HTTP/1.1" 200 674 "-" "curl/7.64.1"
    x.x.x.x - - [14/Aug/2021:18:28:32 +0800] "GET / HTTP/1.1" 200 674 "-" "curl/7.64.1"
    openresty started...
    x.x.x.x - - [14/Aug/2021:18:56:40 +0800] "GET / HTTP/1.1" 200 674 "-" "curl/7.64.1"
    x.x.x.x - - [14/Aug/2021:18:56:40 +0800] "GET / HTTP/1.1" 200 674 "-" "curl/7.64.1"
    x.x.x.x - - [14/Aug/2021:18:56:41 +0800] "GET / HTTP/1.1" 200 674 "-" "curl/7.64.1"
    ```

    The log data of the redeployed **web-windows-smb-\*\*\*\*** container indicates that the log file is retained after the **web-windows-smb-584676df65-t4mmh** container is deleted.


