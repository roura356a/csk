---
keyword: [容器存储, CSI, 存储架构]
---

# 存储CSI概述

阿里云容器服务ACK的容器存储功能基于Kubernetes存储系统，深度融合阿里云存储服务并完全兼容Kubernetes原生的存储服务，例如EmptyDir、HostPath、Secret、ConfigMap等存储。ACK基于社区容器存储接口（CSI）通过部署CSI插件实现了阿里云存储服务接入能力。本文介绍ACK存储CSI的概览、ACK存储CSI支持的功能、CSI使用授权、CSI使用限制等。

## ACK容器存储概览

![overview](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/7749133261/p251924.png)

容器服务ACK支持Pod自动绑定阿里云云盘、NAS、 OSS、CPFS、本地卷等存储服务，每种存储卷的主要特点及应用场景如下表。

|阿里云存储|静态数据卷|动态数据卷|默认部署|主要特点|应用场景|
|-----|-----|-----|----|----|----|
|阿里云云盘|支持|支持|是|非共享存储，每个云盘只能在单个节点挂载。|-   高IO、低延时场景。

云盘块存储设备适用于对IO、延迟要求较高的应用场景，例如：数据库、中间件等服务。

-   非共享场景

云盘卷是独享模式，同时只能给一个Pod使用，在非共享数据业务场景可以考虑使用云盘卷。


更多信息，请参见[t1592119.md\#](/intl.zh-CN/Kubernetes集群用户指南/存储-CSI/云盘存储卷/云盘存储卷概述.md)。 |
|阿里云NAS|支持|支持|是|共享存储，可提供高性能、高吞吐存储服务。|-   共享数据场景

NAS支持多个Pod同时访问一份数据，如果您有共享数据需求，推荐使用NAS。

-   大数据分析场景

NAS提供了较高的数据吞吐能力，能满足大批量作业对共享存储访问的需求。

-   Web应用场景

为Web应用、内容管理系统提供存储支撑。

-   保存日志场景

如果您希望将日志保存到存储卷，推荐使用NAS数据卷。


更多信息，请参见[NAS存储卷概述](/intl.zh-CN/Kubernetes集群用户指南/存储-CSI/NAS存储卷/NAS存储卷概述.md)。 |
|阿里云OSS|支持|不支持|是|共享存储，用户态文件系统。|-   媒体、图片等媒体文件只读场景

您可以通过OSS数据卷读取上述文件类型。

-   网页、应用配置文件只读场景

OSSFS网络性能欠佳，可以支撑一些小文件的读场景。


**说明：** OSS数据卷通过OSSFS挂载，是一种通过FUSE方式模拟实现的用户态文件系统。对于写场景稳定性欠佳，因此在写场景中推荐使用其他存储卷类型。

更多信息，请参见[OSS存储卷概述](/intl.zh-CN/Kubernetes集群用户指南/存储-CSI/OSS存储卷/OSS存储卷概述.md)。 |
|阿里云CPFS|支持|支持|否|高性能、高带宽、共享存储。|-   基因计算、大数据分析场景

CPFS提供的高吞吐能力，满足大规模集群对存储超高性能的需求。

-   数据缓存访问场景

您可以将低速存储数据先下载到CPFS卷，然后供应用高速访问。


更多信息，请参见[CPFS静态卷](/intl.zh-CN/Kubernetes集群用户指南/存储-CSI/CPFS存储卷/CPFS静态卷.md)和[CPFS动态卷](/intl.zh-CN/Kubernetes集群用户指南/存储-CSI/CPFS存储卷/CPFS动态卷.md)。 |

**说明：** ACK部署的CSI插件支持静态存储卷挂载和动态存储卷挂载2种方式，在静态存储卷挂载的方式中，通常需要手动编辑和创建一个PV和PVC进行挂载。当需要PV和PVC数量很大的时候，您可以创建动态存储卷进行挂载。PV和PVC的概念如下：

-   **PersistentVolume（PV）**

    集群内的存储资源。PV独立于Pod的生命周期，可根据不同的StorageClass类型创建不同类型的PV。


-   **PersistentVolumeClaim（PVC）**

    集群内的存储请求。例如，PV是Pod使用的节点资源，PVC则声明使用PV资源。当PV资源不足时，PVC可动态创建PV。


## ACK容器存储功能

ACK不同集群形态与各存储服务能力的对应关系如下表。

|存储分类|存储能力|ACK集群（Linux）|ASK集群|注册集群（混合云／多云）|ACK@Edge|ACK（Windows）|ACK专有云|ACK安全容器（Sandbox）|
|----|----|------------|-----|------------|--------|------------|------|----------------|
|块存储|云盘挂载和卸载|✔️|✔️|❌|❌|✔️|✔️|✔️|
|在线变配（Resize）|✔️|❌|❌|❌|❌|✔️|❌|
|快照（Snapshot）|✔️|✔️|❌|❌|❌|✔️|✔️|
|容器IO监控|✔️|❌|❌|❌|❌|✔️|❌|
|文件系统|支持XFS、 Ext4、dBFS|支持XFS、 Ext4|❌|❌|支持NTFS|支持XFS、 Ext4|支持XFS、 Ext4|
|块、裸设备|✔️|❌|❌|❌|❌|✔️|❌|
|快照恢复|✔️|✔️|❌|❌|❌|✔️|✔️|
|云盘队列设置|✔️|❌|❌|❌|❌|✔️|❌|
|数据加密CMK和BYOK|✔️|✔️|❌|❌|✔️|✔️|✔️|
|多可用区感知|✔️|❌|❌|❌|✔️|✔️|✔️|
|用户自定义标签|✔️|✔️|❌|❌|✔️|✔️|✔️|
|跨主机迁移|✔️|✔️|❌|❌|✔️|✔️|✔️|
|文件存储|NAS创建，挂载，卸载|✔️|✔️|✔️|✔️|❌|✔️|✔️|
|Samba挂载卸载|❌|❌|✔️|❌|✔️|❌|❌|
|CPFS|✔️|❌|✔️|❌|❌|❌|❌|
|NAS回收站|❌|❌|❌|❌|❌|❌|❌|
|动态数据卷-子目录|✔️|❌|✔️|✔️|✔️|✔️|✔️|
|数据加密CMK（极速NAS）|✔️|❌|❌|❌|❌|✔️|✔️|
|容器网络文件系统CNFS|✔️，只支持托管版|❌|❌|❌|❌|❌|❌|
|对象存储|OSS挂载和卸载|✔️|✔️|✔️|✔️|❌|✔️|✔️|
|数据加密BYOK|✔️|✔️|✔️|✔️|❌|✔️|✔️|
|本地存储|块存储LVM|✔️|❌|✔️|✔️|❌|✔️|❌|
|VolumeGroup自动化|✔️|❌|✔️|✔️|❌|✔️|❌|
|节点容量调度|✔️|❌|✔️|✔️|❌|✔️|❌|
|PMEM Direct Mem|✔️|❌|❌|❌|❌|❌|❌|
|PMEM LVM卷|✔️|❌|❌|❌|❌|❌|❌|

## CSI部署架构

CSI插件包括CSI-Plugin和CSI-Provisioner两部分。ACK托管版集群和ACK专有版集群中CSI的部署架构图如下表。

**说明：** ACK托管版和专有版集群默认部署CSI插件。针对ASK集群和边缘集群，您需自行部署CSI插件。

|ACK托管版集群|ACK专有版集群|
|--------|--------|
|![csi managed](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/8748975161/p249741.png)

ACK托管版集群中CSI插件CSI-Provisioner和CSI-Plugin都运行在Worker节点中。

|![flexvolume](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/8748975161/p249742.png)

ACK专有版集群中CSI-Provisioner部署在Master节点上。CSI-Plugin以DaemonSet负载方式自动部署在Master、Worker中。 |

## CSI使用授权

CSI插件在数据卷的挂载、卸载、创建、删除等操作上都需要您授予访问其他产品资源的权限。CSI插件支持直接使用AK授权，也支持使用RAM角色授权的方式（默认）进行授权。两种授权方式的具体内容如下表。

|使用账户AK信息授权|使用RAM角色授权|
|----------|---------|
|-   您可以直接在CSI部署模板中写入AK信息。
-   您也可以通过Secret方式将AK信息传入环境变量。

|ACK集群存储插件CSI使用**AliyunCSManagedCsiRole**角色来访问您在其他云产品中的资源。更多信息，请参见[AliyunCSManagedCsiRole](/intl.zh-CN/Kubernetes集群用户指南/授权/容器服务默认角色.md)。关于如何为RAM角色授权，请参见[为RAM角色授权](/intl.zh-CN/角色管理/为RAM角色授权.md)。

-   托管集群

CSI使用的RAM角色权限的令牌被保存在名为**addon.csi.token**的Secret中，CSI插件挂载该Secret即可以实现RAM角色授权，并访问OpenAPI。

-   专有集群

CSI使用其Pod所在的ECS节点的RAM角色。 |

## CSI插件使用限制

您在使用ACK存储CSI插件时需要注意CSI插件本身的使用限制，也需要注意使用CSI插件时涉及的阿里云存储产品的使用限制。

-   **阿里云存储产品使用限制**

    |阿里云存储产品|使用限制|
    |-------|----|
    |云盘数据卷|    -   每个节点最多挂载15个云盘数据卷。
    -   每个云盘数据卷只能给一个Pod使用，不能共享。
    -   不是每种云盘类型都能挂载所有ECS实例。更多信息，请参见[实例规格族](/intl.zh-CN/实例/实例规格族.md)。
    -   包年包月的云盘不能进行挂载、卸载操作，不能用于容器数据卷场景。
    -   云盘只能挂载到相同可用区的ECS实例，不可以跨可用区挂载。
    -   建议使用StatefulSet方式使用云盘卷，不建议使用Deployment。

**说明：** Deployment是无状态服务，当Pod在重启时，无法保证新Pod启动时间和老Pod结束时间之间的时间叠加，且Deployment多副本模式不能为每个Pod配置独立的存储卷。

    -   每个卷的最小容量是20 GiB。 |
    |NAS数据卷|    -   NAS卷只能挂载到相同VPC的ECS实例，不同VPC网络不能挂载。
    -   每个用户的NAS卷有数量限制，如果您要申请较多数量NAS盘，请[提交工单](https://workorder-intl.console.aliyun.com/console.htm)至文件存储团队。 |
    |OSS数据卷|不推荐您使用OSS数据卷进行数据写操作，写数据场景请选择其他存储。**说明：** OSS数据卷通过OSSFS挂载，是一种通过FUSE方式模拟实现的用户态文件系统。对于写场景稳定性欠佳，因此在写场景中推荐使用其他存储卷类型。 |
    |CPFS数据卷|    -   CPFS驱动对操作系统内核有较强的版本匹配需求，部署完CPFS环境后，请不要升级内核。
    -   当前只支持为没有CPFS驱动的节点安装CPFS驱动，而不支持升级CPFS驱动。
**说明：**

CSI-CPFS插件属于Kubernetes CSI组件，负责挂载CPFS盘到Kubernetes Pod，供应用使用。

CPFS驱动：是CPFS存储产品对应的客户端驱动，为内核层面负责CPFS存储协议的存储实现。

CPFS CSI 插件和CPFS驱动之间的关联：

    -   当节点没有安装CPFS驱动时，如果部署CSI-CPFS插件，会默认安装CPFS驱动。
    -   当节点已经安装CPFS驱动时，如果部署CSI-CPFS插件，不会安装和升级CPFS驱动。 |

-   **ACK存储CSI插件使用限制**

    阿里云CSI插件已在社区开源，主要的支撑场景为ACK集群。对于非ACK集群（非阿里环境集群、阿里云自建集群），由于集群配置、权限管理、网络差异等，插件无法无缝支撑。如果您是这样的集群环境，可以通过阅读源码、修改配置等方式进行适配。更多信息，请参见[alibaba-cloud-csi-driver](https://github.com/kubernetes-sigs/alibaba-cloud-csi-driver/tree/master)。


## 版本说明

使用CSI插件要求Kubernetes版本为1.14及以上，且kubelet运行参数`--enable-controller-attach-detach`需要为`true`。

## CSI插件安装与升级

关于CSI存储插件的安装与升级，请参见[安装与升级CSI组件](/intl.zh-CN/Kubernetes集群用户指南/存储-CSI/安装与升级CSI组件.md)。

## CSI和Flexvolume存储插件的区别

|插件名称|插件特性|参考文档|
|----|----|----|
|Flexvolume|Flexvolume插件是Kubernetes社区较早实现的存储卷扩展机制。ACK从上线起，即支持Flexvolume类型数据卷服务。Flexvolume插件包括以下三部分。 -   Flexvolume：负责数据卷的挂载、卸载功能。ACK默认提供云盘、NAS、OSS三种存储卷的挂载能力。
-   Disk-Controller：负责云盘卷的自动创建能力。
-   Nas-Controller：负责NAS卷的自动创建能力。

|有关Flexvolume的详细概述，请参见[Flexvolume概述](/intl.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/存储Flexvolume概述.md)。

有关如何升级Flexvolume存储插件，请参见[管理组件](/intl.zh-CN/Kubernetes集群用户指南/组件/管理组件.md)。 |
|CSI|CSI插件是当前Kubernetes社区推荐的插件实现方案。ACK集群提供的CSI存储插件兼容社区的CSI特性。CSI插件包括以下两部分： -   CSI-Plugin：实现数据卷的挂载、卸载功能。ACK默认提供云盘、NAS、OSS三种存储卷的挂载能力。
-   CSI-Provisioner：实现数据卷的自动创建能力，目前支持云盘、NAS两种存储卷创建能力。

|有关CSI的详细概述，请参见[CSI概述](/intl.zh-CN/Kubernetes集群用户指南/存储-CSI/存储CSI概述.md)和[alibaba-cloud-csi-driver](https://github.com/kubernetes-sigs/alibaba-cloud-csi-driver)。 |

**说明：**

-   在创建集群的时候确定插件类型。
-   不支持CSI和Flexvolume插件在同一个集群中使用。
-   不支持Flexvolume转变到CSI插件。

**使用推荐**

-   针对新建集群，推荐您使用CSI插件。ACK会跟随社区持续更新CSI插件的各种能力。
-   针对已经创建的集群，仍然使用已经安装的存储插件类型。ACK会持续支持Flexvolume插件。

**如何判断集群的存储插件模式**

-   方式一：通过控制台查看节点注释
    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
    2.  在控制台左侧导航栏中，单击**集群**。
    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。
    4.  在集群管理页左侧导航栏中，选择**节点管理** \> **节点**。
    5.  在目标节点**操作**列选择**更多** \> **详情**。
    6.  在**基本信息**中查看节点**注释**。

        如果有`volumes.kubernetes.io/controller-managed-attach-detach: true`，表示集群存储插件为CSI；若没有`volumes.kubernetes.io/controller-managed-attach-detach: true`，表示集群存储插件为Flexvolume。

-   方式二：通过命令查看kubelet参数

    执行以下命令查看kubelet参数。

    ```
    ps -ef | grep kubelet
    ```

    预期输出：

    ```
    --enable-controller-attach-detach=true
    ```

    若`--enable-controller-attach-detach`的值为`true`，则表示集群存储插件为CSI；若`--enable-controller-attach-detach`的值为`false`，则表示集群存储插件为Flexvolume。


**相关文档**  


[存储基础知识](/intl.zh-CN/Kubernetes集群用户指南/存储-CSI/存储基础知识.md)

[云盘存储卷概述](/intl.zh-CN/Kubernetes集群用户指南/存储-CSI/云盘存储卷/云盘存储卷概述.md)

[NAS存储卷概述](/intl.zh-CN/Kubernetes集群用户指南/存储-CSI/NAS存储卷/NAS存储卷概述.md)

[OSS存储卷概述](/intl.zh-CN/Kubernetes集群用户指南/存储-CSI/OSS存储卷/OSS存储卷概述.md)

[CPFS静态卷](/intl.zh-CN/Kubernetes集群用户指南/存储-CSI/CPFS存储卷/CPFS静态卷.md)

[CPFS动态卷](/intl.zh-CN/Kubernetes集群用户指南/存储-CSI/CPFS存储卷/CPFS动态卷.md)

