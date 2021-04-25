---
keyword: [CPFS共享存储, Arena, 分布式存储, 配置PV和PVC]
---

# 配置CPFS共享存储

阿里云文件存储CPFS（Cloud Paralleled File System）是一种并行文件系统。CPFS的数据存储在集群中的多个数据节点，并可由多个客户端同时访问，从而能够为大型高性能计算机集群提供高IOPS、高吞吐、低时延的数据存储服务。文本介绍如何配置CPFS共享存储。

为了保留数据科学家的工作内容，或者读取同一份训练数据。建议您配置共享存储卷，并挂载到Arena提交作业的运行环境中，确保数据科学家的工作内容（代码、数据）得以保留，不会随着容器删除而丢失。

在团队开发中，建议分配一个共享的存储池，让数据和代码能够在团队中共享。在Arena提交作业使用--data参数时，如果您声明了配置共享存储，以及要挂载到运行环境的路径，共享存储将会被挂载到您指定的目录中，这样提交作业就可以复用这部分数据或者代码。

在Kubernetes中，通过存储卷（PV）和存储声明（PVC）描述存储对象。作为集群管理员，分配环境时您需要为每个数据科学家创建属于自己的存储声明。例如用户A和用户B，存储声明的后端可以挂载到相同的NAS或者CPFS，但是必须指定不同的子目录，保证他们工作环境隔离。

## 步骤一：创建CPFS实例

有关创建CPFS实例的具体操作步骤，请参见[创建文件系统]()。

**说明：**

创建CPFS实例的参数配置，例如地域、专有网络、虚拟交换机，需和ACK集群的参数配置一致。

## 步骤二：配置挂载点

1.  登录[NAS控制台](https://nasnext.console.aliyun.com/)。

2.  在控制台左侧导航栏，选择**文件系统** \> **文件系统列表**。

3.  在目标CPFS实例右侧的**操作**列下，单击**管理**。

    在**基本信息**页签，查看文件系统ID和挂载点信息。

    ![CPFS](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4704359951/p134706.png)


## 步骤三：配置ACK集群的存储卷（PV）和存储声明（PVC）

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

5.  在无状态（Deployment）页面，单击右上角**使用YAML创建资源**。

    设置集群名称和命名空间、设置示例模板为**Resource-PersistentVolumeClaim**。

    模板内容如下。

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

    **说明：**

    PV和PVC的配置说明如下：

    -   server：填写[步骤二：配置挂载点](#section_dhs_svi_aui)中获取的CPFS挂载点信息。
    -   fileSystem：填写CPFS文件系统ID。
    -   subPath：填写tf\_data。
6.  单击**创建**。

    通过YAML模板成功创建存储卷（PV）和存储声明（PVC）后，在**存储卷**列表和**存储声明**列表分别看到创建好的CPFS存储卷（PV）和存储声明（PVC）。


