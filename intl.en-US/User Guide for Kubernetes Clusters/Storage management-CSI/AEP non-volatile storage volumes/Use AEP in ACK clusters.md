---
keyword: [PMEM, Non-Volatile Memory \(NVM\), Optane DIMMs, Intel Optane DC Persistent Memory \(Apache Pass - AEP\)]
---

# Use AEP in ACK clusters

Traditional memory and storage products have limits on density, performance, and cost. Persistent memory \(PMEM\) is a storage product that provides high-performance, persistent, and in-memory storage services. Apache Pass \(AEP\) is a PMEM product developed by Intel. It integrates cutting edge memory and storage technologies. AEP expands memory capacity in a cost-effective manner and enables low-latency access to persisted data. You can use AEP in Container Service for Kubernetes \(ACK\) clusters. This topic describes how to use AEP in ACK clusters by using an example.

## AEP overview

In recent years, CPU technology has rapidly developed. The increase in computing frequency of single-core CPUs and the emergence of multi-core CPUs have made a considerable contribution to the development of the computing capabilities of CPUs. However, storage media as data carriers have not kept up with the development of CPUs. Dynamic random-access memory \(DRAM\) provides high-speed reads and writes but loses data when power is off. In addition, DRAM does not apply to large-scale scenarios due to its high cost. NVM \(such as SSDs and disks\) does not lose data when power is off. However, they cannot provide high-speed reads and writes as required. Therefore, the computing industry has been in research for a memory product that can meet the computing requirements of CPUs and avoid data loss when power is off.

The release of AEP indicates that PMEM is becoming a mature technology. PMEM products provide high-speed reads and writes that are almost the same as those by using memory. PMEM products are being used in data acceleration industries.

The following figure shows the hierarchy of memory and storage media based on the cost, latency, and capacity. You can view the position of PMEM in the hierarchy.

![AEP](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9236065061/p170018.png)

## How to use AEP in ACK

You can use the Container Storage Interface \(CSI\) driver provided by Alibaba Cloud to manage the lifecycle of AEP devices in ACK clusters. This allows you to allocate, mount, and use AEP resources through declarative claims.

You can use AEP in ACK clusters by using the following methods:

-   **PMEP-LVM \(use AEP as non-intrusive block storage\)**

    You can directly claim the AEP resources without modifying your applications. You can use Logical Volume Manager \(LVM\) to virtualize PMEM resources on a node into volume groups \(VGs\). Then, you can create persistent volume claims \(PVCs\) of the required type and capacities. You can use AEP without modifying the following types of applications: serverless applications, low-latency and high-throughput data computing applications, and short-CI/CD period applications that require high-speed temporary storage. This allows you to improve the I/O throughput by 2 to 10 times.

-   **PMEM-direct memory**

    You can use PMEM as direct memory by making a specific number of modifications to the memory allocation functions. This allows you to access data in almost the same way as using dynamic random access memory \(DRAM\). This way, you can provision AEP resources as direct memory at TB-level and reduce 30% to 50% of the cost. This meets the requirements of in-memory databases such as Redis and SAP HANA in terms of large memory and cost-effectiveness.


**Note:**

PMEP-LVM: AEP resources can be used as block storage or file systems in ACK clusters without intrusion or modification to your applications. The I/O throughput is 2 to 10 times higher than SSDs.

PMEM-direct memory: AEP resources can be used as direct memory in ACK clusters. You must modify the applications so that they are adaptive to the logic in PMEM SDK for memory allocation. This offers high throughput and low latency that are comparable to DRAM.

|Method|Support for fragmented storage|Support for online expansion|Support for memory persistence|Application modifications|Latency \(4K/RW\)|Throughput \(4K/RW\)|Maximum capacity of a single ECS instance \(ecs.ebmre6p.26xlarge\)|
|------|------------------------------|----------------------------|------------------------------|-------------------------|-----------------|--------------------|------------------------------------------------------------------|
|PMEM-LVM|No|Yes|Yes|No|10 us|10W|1536GB|
|PMEM-Direct|Yes|No|No|Yes|1.2 us|56W|768GB|
|SSD|No|Yes|Yes|No|100 us|1W|32TB|

## Deploy the CSI plug-in for memory volumes

To use AEP in ACK clusters, you must deploy the following components:

-   CSI-Plugin: initializes PMEM devices and creates, deletes, mounts, and unmounts volumes.
-   CSI-Provisioner: detects and initiates volume creation and deletion requests.
-   CSI-Scheduler: schedules storage \(the ACK scheduler is preinstalled\).

**Note:**

When you deploy CSI-Plugin, take note of the following limits:

-   To enable automatic operations and maintenance \(O&M\) for AEP devices, you must add the `pmem.csi.alibabacloud.com` label to the node that uses AEP.
-   To use the PMEP-LVM method, you must add the `pmem.csi.alibabacloud.com: lvm` label to the node that uses AEP.
-   To use the PMEM-direct memory method, you must add the `pmem.csi.alibabacloud.com: direct` label to the node that uses AEP.

1.  Create an ACK cluster.

    Create an ACK cluster that contains ECS instances with AEP resources, for example, ecs.ebmre6p.26xlarge. For more information about how to create an ACK cluster, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

2.  Configure the node with AEP resources.

    To enable the CSI plug-in to function as expected, you must add the required label to the node.

    Add the following label:

    ```
    pmem.csi.alibabacloud.com/type: direct
    ```

    or add the following label:

    ```
    pmem.csi.alibabacloud.com/type: lvm
    ```

3.  Deploy the CSI plug-in for PMEM.




## Examples

1.  **Use AEP as block storage volumes**

    1.  Create a PVC.

        ```
        apiVersion: v1
        kind: PersistentVolumeClaim
        metadata:
          annotations:
            volume.kubernetes.io/selected-node: cn-zhangjiakou.192.168.1.12
          name: pmem-lvm
        spec:
          accessModes:
          - ReadWriteOnce
          resources:
            requests:
              storage: 10Gi
          storageClassName: pmem-lvm
        ```

        To schedule the PVC to a specific AEP node, add the following annotation to the PVC configurations: `annotations: volume.kubernetes.io/selected-node`.

    2.  Deploy an application.

        ```
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: deployment-lvm
          labels:
            app: nginx-lvm
        spec:
          selector:
            matchLabels:
              app: nginx-lvm
          template:
            metadata:
              labels:
                app: nginx-lvm
            spec:
              containers:
              - name: nginx
                image: nginx:1.7.9
                command: ["sh", "-c"]
                args: ["sleep 10000"]
                volumeMounts:
                  - name: pmem-pvc
                    mountPath: "/data"
              volumes:
                - name: pmem-pvc
                  persistentVolumeClaim:
                    claimName: pmem-lvm
        ```

    3.  Check the result.

        ```
        kubectl get pvc 
        ```

        ```
        pmem-lvm           Bound    disk-334cb6fa-fabd-4687-96dc-0d2b15564822   10Gi       RWO            pmem-lvm                  10m
        ```

        ```
        kubectl get pod
        ```

        ```
        NAME                                READY   STATUS    RESTARTS   AGE
        deployment-lvm-5bf65c7f76-jb6nl     1/1     Running   0          10m
        ```

        ```
        kubectl exec -ti deployment-lvm-5bf65c7f76-jb6nl sh
        ```

        ```
        df /data
        ```

        ```
        Filesystem                                                               1K-blocks  Used Available Use% Mounted on
        /dev/mapper/pmemvgregion0-disk--334cb6fa--fabd--4687--96dc--0d2b15564822  10255636 36888  10202364   1% /data
        ```

        The output shows that a block storage volume is created and mounted to the application pod.


1.  **Use AEP as direct memory volumes**

    1.  Create a PVC.

        ```
        apiVersion: v1
        kind: PersistentVolumeClaim
        metadata:
          annotations:
            volume.kubernetes.io/selected-node: cn-zhangjiakou.192.168.1.14
          name: pmem-direct
        spec:
          accessModes:
          - ReadWriteOnce
          resources:
            requests:
              storage: 9Gi
          storageClassName: pmem-direct
        ```

        To schedule the PVC to a node with AEP resources, add the following annotation to the PVC configurations: `annotations: volume.kubernetes.io/selected-node`.

    2.  Deploy an application.

        ```
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: deployment-direct
          labels:
            app: nginx-direct
        spec:
          selector:
            matchLabels:
              app: nginx-direct
          template:
            metadata:
              labels:
                app: nginx-direct
            spec:
              containers:
              - name: nginx
                image: nginx:1.7.9
                command: ["sh", "-c"]
                args: ["sleep 1000"]
                volumeMounts:
                  - name: pmem-pvc
                    mountPath: "/data"
              volumes:
                - name: pmem-pvc
                  persistentVolumeClaim:
                    claimName: pmem-direct
        ```

    3.  Check the result.

        ```
        kubectl get pvc pmem-direct
        ```

        ```
        NAME          STATUS   VOLUME                                      CAPACITY   ACCESS MODES   STORAGECLASS   AGE
        pmem-direct   Bound    disk-15c75a5d-469e-4dd7-b67e-6bdc489288a2   9Gi        RWO            pmem-direct    17m
        ```

        ```
        kubectl get pod
        ```

        ```
        NAME                                READY   STATUS    RESTARTS   AGE
        deployment-direct-64d448d96-9znfk   1/1     Running   0          17m
        ```

        ```
        kubectl exec -ti deployment-direct-64d448d96-9znfk sh
        ```

        ```
        df /data
        ```

        ```
        Filesystem     1K-blocks  Used Available Use% Mounted on
        /dev/pmem0       9076344 36888   9023072   1% /data
        ```

        The output shows that a PMEM volume is created and mounted to the application pod.


