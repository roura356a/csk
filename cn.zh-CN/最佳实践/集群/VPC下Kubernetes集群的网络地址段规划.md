# VPC下Kubernetes集群的网络地址段规划

在创建Kubernetes集群时，需要指定专有网络VPC、虚拟交换机、 Pod网络CIDR（地址段）和Service CIDR（地址段）。因此建议您提前规划ECS地址、Kubernetes Pod地址和Service地址。本文将介绍阿里云VPC环境下Kubernetes集群里各种地址的作用，以及地址段该如何规划。

## VPC网段和Kubernetes网段关系

VPC网段规划包含VPC自身网段和虚拟交换机网段，Kubernetes网段规划包含Pod地址段和Service地址段。二者的关系示例，如下图所示。

![Kubernetes网段和VPC网段关系](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4575659951/p8764.png)

具体概念和注意事项的介绍，如下所示：

-   VPC网段。

    您在创建VPC时选择的网段。只能从10.0.0.0/8、172.16.0.0/12、192.168.0.0/16三者当中选择一个。

-   交换机网段。

    在VPC里创建交换机时指定的网段，必须是当前VPC网段的子集（可以跟VPC网段一样，但不能超过）。

    -   交换机下面的ECS所分配到的地址，就是从这个交换机网段内获取的。
    -   一个VPC下，可以创建多个交换机，但交换机网段不能重叠。
-   Pod地址段。

    Pod是Kubernetes内的概念，每个Pod具有一个IP地址。

    -   Pod地址段不能和VPC网段重叠。
    -   Pod地址段不能和Service地址段重叠。
    例如，VPC网段用的是172.16.0.0/12，Kubernetes的Pod地址段就不能使用172.16.0.0/16、172.17.0.0/16等，因为这些地址都包含在172.16.0.0/12里了。

-   Service地址段。

    Service是Kubernetes内的概念，对应的是`Type=ClusterIP`时Service使用的地址，每个Service有自己的地址。

    -   Service地址只在Kubernetes集群内使用，不能在集群外使用。
    -   Service地址段不能和VPC网段重叠。
    -   Service地址段不能和Pod地址段重叠。

## 如何选择地址段

-   **场景1：单VPC+单Kubernetes集群**

    这是最简单的情形。VPC地址在创建VPC的时候就已经确定，创建Kubernetes集群时，选择的Pod及Service地址段和当前VPC不一样的地址段即可。

-   **场景2：单VPC+多Kubernetes集群**

    一个VPC下创建多个Kubernetes集群。

    -   VPC地址是在创建VPC时已经确定。创建Kubernetes集群时，每个集群内的VPC地址段、Service地址段和Pod地址段彼此间不能重叠。
    -   所有Kubernetes集群之间的Pod地址段不能重叠，但Service地址段可以重叠。
    -   在默认的网络模式下（Flannel），Pod的报文需要通过VPC路由转发，容器服务会自动在VPC路由上配置到每个Pod地址段的路由表。
    **说明：** 这种情况下Kubernetes集群部分互通，一个集群的Pod可以直接访问另外一个集群的Pod和ECS，但不能访问另外一个集群的Service。

-   **场景3：VPC互联**

    两个VPC网络互联的情况下，可以通过路由表配置哪些报文要发送到对端VPC里。如下表所示，VPC 1使用地址段192.168.0.0/16，VPC 2使用地址段172.16.0.0/12，您可以通过路由表，指定在VPC 1里把目的地址为172.16.0.0/12的报文都发送到VPC 2。

    ![路由表](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4575659951/p8765.png)

    |类别|地址段|目的端|转发到|
    |--|---|---|---|
    |VPC 1|192.168.0.0/16|172.16.0.0/12|VPC 2|
    |VPC 2|172.16.0.0/12|192.168.0.0/16|VPC 1|

    在这种情况下，VPC 1里创建的Kubernetes集群有以下限制：

    -   不能和VPC 1的地址段重叠
    -   不能和VPC 2的地址段重叠
    -   不能和其他集群的地址段重叠
    -   不能和Pod的地址段重叠
    -   不能和Service的地址段重叠
    在VPC 2创建Kubernetes集群也是如此。

    这个例子中，Kubernetes集群Pod地址段可以选择10.0.0.0/8下的某个子段。

    **说明：** 这里要特别关注转发到VPC 2的地址段，可以把这部分地址理解成已经占用的地址，Kubernetes集群不能和已经占用的地址重叠。

    如果VPC 2里要访问VPC 1的Kubernetes Pod，则需要在VPC 2里配置到VPC1 Kubernetes集群Pod地址的路由。

-   **场景4：VPC网络到IDC**

    和VPC互联场景类似，同样存在VPC里部分地址段路由到IDC，Kubernetes集群的Pod地址就不能和这部分地址重叠。IDC里如果需要访问Kubernetes里的Pod地址，同样需要在IDC端配置到专线VBR的路由表。


