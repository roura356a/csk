---
keyword: [云盘监控, csi-plugin, Prometheus, 事件告警]
---

# 使用csi-plugin组件监控节点侧存储资源

容器服务Kubernetes版ACK（Container Service for Kubernetes）的存储监控可以帮助您在使用云盘和NAS时，通过阿里云Prometheus服务监控当前节点侧存储的使用状况。本文介绍如何使用ACK的节点侧存储监控功能，并举例说明如何配置事件告警。

目前只有v1.18.8.46-afb19e46-aliyun版本的csi-plugin同时支持云盘监控功能及NAS监控功能，请确保当前的csi-plugin组件的YAML版本和镜像版本为最新版本。您可以通过以下方式升级csi-plugin至最新版。

-   执行GitHub社区中[alibaba-cloud-csi-driver](https://github.com/kubernetes-sigs/alibaba-cloud-csi-driver/tree/master/deploy/ack/upgrade)的升级脚本。
-   通过控制台升级。具体操作，请参见[通过控制台升级csi-plugin组件](#section_y7k_ts0_p79)。

在阿里云ACK集群中通常使用云盘数据卷或NAS数据卷存储数据，通过部署csi-plugin的最新版本（ACK集群默认安装csi-plugin组件）可以将磁盘监控与PVC进行绑定。通过阿里云Prometheus监控可以查看当前PVC的状态、配置容量阈值和IOPS阈值，进行事件告警以感知当前PVC使用情况。

## 通过控制台升级csi-plugin组件

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理详情页左侧导航栏，选择**运维管理** \> **组件管理**。

5.  单击存储页签，在**csi-plugin**组件区域单击**升级**。


## 存储监控使用说明

目前节点侧监控功能支持云盘及NAS两种存储产品。您可以通过阿里云Prometheus服务监控当前云盘或NAS的使用状况，具体操作如下。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理详情页左侧导航栏，选择**运维管理** \> **Prometheus监控**。

5.  单击**CSI Nodes**监控大盘名称，进入csi-plugin组件节点侧的监控大盘，查看监控信息。

    监控信息如下图。

    ![metrics](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/3121098161/p183549.png)


**CSI Nodes**监控大盘过滤项及监控指标说明如下：

-   过滤项：

    |过滤项|说明|
    |---|--|
    |**StorageType**|存储类型，disk表示云盘，nas表示NAS文件存储。|
    |**Namespace**|PVC所在的命名空间|
    |**PVC**|PVC的名称|

-   监控指标：

    |指标名称|说明|显示单位|
    |----|--|----|
    |IOPS|PVC的读写IOPS|ops/sec（每秒读写磁盘次数）|
    |Latency\(avg\)|PVC的读写请求的平均时延|ms（毫秒）|
    |ThroughPut|PVC的吞吐量|GB|
    |IO Count In Queue|IO请求在磁盘队列的个数|个数|
    |Used Capacity|PVC的磁盘使用空间大小|GB|
    |Total Capacity|PVC的磁盘总空间大小|GB|
    |Free Capacity|PVC的磁盘剩余空间大小|GB|


## 云盘存储监控使用示例

查看云盘存储监控资源需要开启阿里云Prometheus监控。具体操作，请参见[开启阿里云Prometheus监控](/cn.zh-CN/Kubernetes集群用户指南/可观测性/监控管理/阿里云Prometheus监控.md)。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理详情页左侧导航栏，选择**运维管理** \> **Prometheus监控**。

5.  在Prometheus监控页面单击**CSI Nodes**页签。

6.  在CSI Nodes页签，选择云盘存储卷所在的**StorageType**、**Namespace**及PVC名称后，在**Total Capacity**区域可查看云盘存储卷当前容量。

    本示例**StorageType**选择为**disk**、**Namespace**选择为**default**、**PVC**为**pvc-disk-static-create1**。

    ![云盘存储监控资源](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/1173811361/p325538.png)

    从上图可得，当前云盘总容量为39.25 GiB，使用了48.2 MiB，剩余39.18 GiB。

7.  向云盘中写入测试数据。

    1.  执行以下命令查看挂载云盘的应用名称。

        ```
        kubectl get pod
        ```

        预期输出：

        ```
        NAME                                              READY   STATUS    RESTARTS   AGE
        deployment-disk-static-create1-67f4b5cfdc-p5q7t   1/1     Running   0          20m
        ```

    2.  执行以下命令进入挂载云盘的应用。

        ```
        kubectl exec deployment-disk-static-create1-67f4b5cfdc-p5q7t -ti sh
        ```

    3.  执行以下命令向云盘挂载目录/data写入20 GiB数据。

        ```
        cd /data
        /usr/bin/fio -direct=1 -iodepth=256 -rw=randwrite -ioengine=libaio -bs=4k -numjobs=1 -runtime=100 -group_reporting -name=Rand_Write_Testing -filename=./test -size=20G
        ```

8.  在Prometheus监控页面的**CSI Nodes**页签，再次查看云盘存储监控资源。

    ![云盘存储监控数据写入后](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/1173811361/p325634.png)

    从上图可得，写入的IOPS为4 k，吞吐率为15 MB/s，写入数据容量为20 GiB，剩余容量为19 GiB，平均延时约为67 ms。


## NAS存储监控使用示例

查看NAS存储监控资源需要开启阿里云Prometheus监控。具体操作，请参见[开启阿里云Prometheus监控](/cn.zh-CN/Kubernetes集群用户指南/可观测性/监控管理/阿里云Prometheus监控.md)。

1.  向NAS存储卷挂载目录写入测试数据。

    1.  执行以下命令查看挂载NAS存储卷的应用名称。

        ```
        kubectl get pod
        ```

        预期输出：

        ```
        NAME                                   READY    STATUS    RESTARTS   AGE
        cnfs-nas-deployment-84f6cdf6cc-wvjjq   1/1      Running   0          20m
        ```

    2.  执行以下命令进入挂载NAS存储卷的应用。

        ```
        kubectl exec cnfs-nas-deployment-84f6cdf6cc-wvjjq -ti sh
        ```

    3.  执行以下命令向NAS存储卷挂载目录/data写入20 GiB数据。

        ```
        cd /data
        /usr/bin/fio -direct=1 -iodepth=256 -rw=randwrite -ioengine=libaio -bs=4k -numjobs=1 -runtime=100 -group_reporting -name=Rand_Write_Testing -filename=./test -size=20G
        ```

2.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

3.  在控制台左侧导航栏中，单击**集群**。

4.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

5.  在集群管理详情页左侧导航栏，选择**运维管理** \> **Prometheus监控**。

6.  在Prometheus监控页面单击**CSI Nodes**页签。

7.  在CSI Nodes页签，选择NAS存储卷所在的**StorageType**、**Namespace**及PVC名称后，在**Total Capacity**区域可查看NAS存储卷当前容量。

    本示例**StorageType**选择为**nas**、**Namespace**选择为**default**、**PVC**为**cnfs-nas-pvc**。

    ![NAS存储监控资源](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/0373811361/p325713.png)

    从上图可得，写入的IOPS约为15 k，吞吐率从10 MB/S增长到60 MB/s，平均延时约为15 ms。


