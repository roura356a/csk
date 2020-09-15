# VPC下Kubernetes的网络地址段规划

在阿里云上创建Kubernetes集群时，通常情况下，可以选择自动创建专有网络，使用默认的网络地址即可。在某些复杂的场景下，需要您自主规划ECS地址、Kubernetes Pod地址和Service地址。本文将介绍阿里云VPC环境下Kubernetes里各种地址的作用，以及地址段该如何规划。

## Kubernetes网段基本概念

-   VPC网段。您在创建VPC选择的地址段。只能从10.0.0.0/8、172.16.0.0/12、192.168.0.0/16三者当中选择一个。
-   交换机网段。在VPC里创建交换机时指定的网段，必须是当前VPC网段的子集（可以跟VPC网段地址一样，但不能超过）。交换机下面的ECS所分配到的地址，就是从这个交换机地址段内获取的。一个VPC下，可以创建多个交换机，但交换机网段不能重叠。
-   Pod地址段。Pod是Kubernetes内的概念，每个Pod具有一个IP地址。在阿里云容器服务上创建Kubernetes集群时，可以指定Pod的地址段，不能和VPC网段重叠。例如VPC网段用的是172.16.0.0/12，Kubernetes的Pod网段就不能使用172.16.0.0/16、172.17.0.0/16等，因为这些地址都涵盖在172.16.0.0/12里了。
-   Service地址段。Service也是Kubernetes内的概念，每个Service有自己的地址。同样，Service地址段也不能和VPC地址段重合，而且Service地址段也不能和Pod地址段重合。Service地址只在Kubernetes集群内使用，不能在集群外使用。

VPC网段结构如下图。

![VPC网段结构](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4575659951/p8763.png)

Kubernetes网段和VPC网段关系如下图。

![Kubernetes网段和VPC网段关系](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4575659951/p8764.png)

## 如何选择地址段

-   单VPC+单Kubernetes集群场景

    这是最简单的情形。VPC地址在创建VPC的时候就已经确定，创建Kubernetes集群时，选择的Pod及Service地址段和当前VPC不一样的地址段即可。

-   单VPC+多Kubernetes集群场景

    一个VPC下创建多个Kubernetes集群。在默认的网络模式下（Flannel），Pod的报文需要通过VPC路由转发，容器服务会自动在VPC路由上配置到每个Pod地址段的路由表。所有Kubernetes集群的Pod地址段不能重叠，但Service地址段可以重叠。

    VPC地址是创建VPC的时候确定的，创建Kubernetes时，为每个Kubernetes集群选择一个不重叠的地址段，不仅不能和VPC地址重叠，也不和其他Kubernetes Pod段重叠。

    **说明：** 这种情况下Kubernetes集群部分互通，一个集群的Pod可以直接访问另外一个集群的Pod和ECS，但不能访问另外一个集群的Service。

-   VPC互联场景

    两个VPC网络互联的情况下，可以通过路由表配置哪些报文要发送到对端VPC里。如下表所示，VPC 1使用地址段192.168.0.0/16，VPC 2使用地址段172.16.0.0/12，我们可以通过路由表，指定在VPC 1里把目的地址为172.16.0.0/12的报文都发送到VPC 2。

    ![路由表](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4575659951/p8765.png)

    |类别|地址段|目的端|转发到|
    |--|---|---|---|
    |VPC 1|192.168.0.0/16|172.16.0.0/12|VPC 2|
    |VPC 2|172.16.0.0/12|192.168.0.0/16|VPC 1|

    在这种情况下，VPC 1里创建的Kubernetes集群有以下限制：

    -   不能和VPC1的地址段重叠
    -   不能和VPC2的地址段重叠
    -   不能和其他集群的地址段重叠
    -   不能和Pod的地址段重叠
    -   不能和Service的地址段重叠
    在VPC 2创建Kubernetes集群也是如此。

    这个例子中，Kubernetes集群Pod地址段可以选择10.0.0.0/8下的某个子段。

    **说明：** 这里要特别关注转发到VPC 2的地址段，可以把这部分地址理解成已经占用的地址，Kubernetes集群不能和已经占用的地址重叠。

    如果VPC 2里要访问VPC 1的Kubernetes Pod，则需要在VPC 2里配置到VPC1 Kubernetes集群Pod地址的路由。

-   VPC网络到IDC的场景

    和VPC互联场景类似，同样存在VPC里部分地址段路由到IDC，Kubernetes集群的Pod地址就不能和这部分地址重叠。IDC里如果需要访问Kubernetes里的Pod地址，同样需要在IDC端配置到专线VBR的路由表。


