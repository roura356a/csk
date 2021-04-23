---
keyword: [PMEM, 非易失性存储器NVM, Optane DIMMs, Intel Optane DC Persistent Memory（Apache Pass - AEP ）]
---

# ACK支持AEP非易失性数据卷

过去内存和存储产品一直受到密度、性能和成本的限制。AEP（Apache Pass），由Intel推出的一种PMEM（持久化内存是一类高性能、数据可持久、内存存储的统称）产品，通过经济地扩展内存容量，并增加对持久数据的低延迟访问，从而将一流的内存和存储特性集成于一个产品中。阿里云容器服务ACK支持使用AEP非易失性存储卷。本文介绍ACK中使用AEP非易失性存储的方式和使用示例。

## AEP概述

近年来CPU技术发展迅速，单核CPU计算频率增加、多核CPU技术的出现让计算能力得到长足发展。相比之下，作为数据载体的存储介质，却没有跟上发展的速度：内存（DRAM）可以提供优异的访问性能，但掉电数据丢失，价格昂贵影响了对大容量内存使用场景，而非易失性存储（SSD、磁盘）在时延上无法满足极致性能计算的需求。为此业界一直在探索既能满足CPU计算性能要求、又能保证数据掉电不丢失属性的产品。

既兼顾非易失性存储，又接近内存的使用方式和速度的新型硬件Intel Optane DC Persistent Memory（Apache Pass - AEP ）的发布标志持久化内存Persistent Memory（PMEM）技术已经进入成熟阶段，并开始进入到各种数据加速领域。

下图为AEP的PMEM（Persistent Memory）在存储层次结构中的位置。

![AEP](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4934101061/p170018.png)

## ACK中AEP非易失性存储卷的使用方式

目前ACK集群已经支持AEP设备的生命周期管理，通过Alibaba Cloud CSI Driver，您可以以声明式的方式对AEP资源进行分配、挂载、使用。

在ACK中，使用AEP非易失性存储卷的方式有以下两种：

-   **PMEP-LVM方式（无侵入的块存储使用方式）**

    您无需修改应用，直接声明即可使用。此方案的原则是把节点上PMEM资源统一成VolumeGroup，然后通过声明PVC类型和容量，使用PMEP-LVM。对无服务器化应用，CICD短生命周期高速临时存储，以及低延迟高吞吐的数据类应用，无需修改应用，即可实现IO性能的2~10倍的提升。

-   **PMEM-直接内存（Direct Memory）访问方式**

    为了达到极致的，可以媲美DRAM的访问性能，对应用的内存分配部分函数做有限的修改，可以实现对PMEM设备的直接访问。对内存数据库Redis、HANA等大内存低成本需求，同时满足对TB级别的DRAM和成本的诉求，降低了内存成本30%~50%。


**说明：**

逻辑卷管理LVM方式：在ACK环境下，非易失性存储作为块存储或文件系统方式访问，无任何应用侵入和修改，读写性能相比SSD提升2~10倍。

直接内存方式：在ACK环境下，非易失性存储作为直接内存访问，需要修改应用适配PMEM SDK做内存分配的代码，达到接近内存访问的吞吐和时延。

|方式|碎片化|在线扩容|持久化|应用修改|时延 \(4K/RW\)|吞吐 （4K/RW\)|单盘最大容量（ecs.ebmre6p.26xlarge）|
|--|---|----|---|----|------------|-----------|----------------------------|
|PMEM-LVM|无|支持|是|否|10 us|10W|1536GB|
|PMEM-Direct|是|否|否|是|1.2 us|56W|768GB|
|SSD|无|支持|是|否|100 us|1W|32TB|

## 插件部署

ACK对非易失性内存支持方案的系统组件包含：

-   CSI-Plugin：实现PMEM设备初始化，卷的具体创建、删除操作，实现挂载、卸载操作。
-   CSI-Provisioner：感知并发起卷的创建、删除操作。
-   CSI-Scheduler：容量调度 （ACK调度器内置）。

**说明：**

在部署CSI-Plugin时，您需注意：

-   在节点加上标签`pmem.csi.alibabacloud.com`后才会启用AEP设备自运维。
-   `pmem.csi.alibabacloud.com: lvm`表示此节点使用LVM方式提供PV卷。
-   `pmem.csi.alibabacloud.com: direct`表示此节点使用内存访问方式提供PV卷。

1.  创建ACK集群。

    ACK集群中添加AEP的PMEM类型的ECS节点实例，例如：ecs.ebmre6p.26xlarge。有关创建集群的具体步骤请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。

2.  配置AEP的PMEM节点类型。

    您所添加的AEP节点需要配置上相应的标签，方可启用PMEM CSI的挂载能力。

    给节点添加以下Label。

    ```
    pmem.csi.alibabacloud.com/type: direct
    ```

    或：

    ```
    pmem.csi.alibabacloud.com/type: lvm
    ```

3.  部署CSI PMEM插件。




## 使用示例

1.  **创建块存储类型数据卷**

    1.  创建PVC。

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

        为了定向将PVC调度到某个AEP节点，可以配置`annotations: volume.kubernetes.io/selected-node`。

    2.  创建应用负载。

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

    3.  查看结果。

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

        可见已经动态创建了一个块存储数据卷，并挂载给Pod使用。


1.  **创建直接内存类型数据卷**

    1.  创建PVC。

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

        为了定向将PVC调度到某个直接内存类型的AEP节点，可以配置`annotations: volume.kubernetes.io/selected-node`。

    2.  创建应用负载。

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

    3.  查看结果。

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

        可见已经动态创建了一个PMEM数据卷，并挂载给Pod使用。


