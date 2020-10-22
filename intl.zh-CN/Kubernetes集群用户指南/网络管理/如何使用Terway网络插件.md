# 如何使用Terway网络插件

本文介绍如何使用阿里云容器服务Kubernetes版集群的Terway网络插件。

## Terway网络插件

Terway网络插件是阿里云容器服务自研的网络插件，使用原生的弹性网卡分配给Pod实现Pod网络：

-   将阿里云的弹性网卡和辅助IP分配给容器。
-   支持基于Kubernetes标准的Network Policy来定义容器间的访问策略，兼容Calico的Network Policy。

![Terway网络插件](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4385659951/p32414.png)

在Terway网络插件中，每个Pod拥有自己网络栈和IP地址。同一台ECS内的Pod之间通信，直接通过机器内部的转发，跨ECS的Pod通信，报文通过VPC的弹性网卡直接转发。由于不需要使用VxLAN等的隧道技术封装报文，因此具有较高的通信性能。

## 注意事项

-   如果要使用Terway插件，建议您选择较高规格和较新类型的ECS机型，即5代或者6代的8核以上机型。
-   单节点所支持的最大Pod数取决于该节点的弹性网卡（ENI）数。

    -   共享ENI支持的最大Pod数 =（ECS支持的ENI数-1）×单个ENI支持的私有IP数
    -   独占ENI支持的最大Pod数 = ECS支持的ENI数－1
    **说明：** 在创建集群或扩容集群的选择实例规格区域，可以查看ECS支持的ENI数量。


## 使用Terway网络插件

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  单击页面右上角的**创建集群**。

4.  在选择集群模板页面，单击**标准版集群**。

    **说明：** 本文以创建标准托班集群为例，具体步骤请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes托管版集群.md)。

5.  设置启动用的网络插件，选择**Terway**。

    ![设置网络插件](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/9888232061/p32375.png)

    网络插件选择**Terway**时，需要配置**Terway模式**：

    -   设置是否选中**Pod独占弹性网卡以获得最佳性能**

-   如果选中，Pod将独占一个专有的弹性网卡。
-   如果不选中，使用弹性网卡的辅助IP分配给Pod，一个Pod占用一个弹性网卡辅助IP地址。
    -   设置是否选中**IPvlan**

-   只在弹性网卡共享模式支持选中。
-   如果选中，采用IPVLAN+eBPF作为网卡共享模式虚拟化技术，并且只能使用Alibaba Cloud Linux 2系统，性能优于默认模式。
-   如果不选中，则使用默认模式，采用策略路由作为网卡共享模式虚拟化技术，同时兼容Centos 7和Alibaba Cloud Linux 2的系统。
**说明：** 当前只有白名单用户可使用上述**Pod独占弹性网卡以获得最佳性能**和**IPvlan**两种功能。[提交工单](https://workorder-intl.console.aliyun.com/console.htm)申请使用。

    -   设置是否选中**NetworkPolicy支持**

-   只在弹性网卡共享模式下支持选中，默认不选中。
-   如果选中，集群支持使用Kubernetes的NetworkPolicy策略对Pod进行网络控制。
-   如果不选中，集群将不会支持使用Kubernetes的NetworkPolicy策略对Pod进行网络控制，这样将不存在网络策略对Kubernetes的API Server产生过大的负载。

## Flannel与Terway

在创建Kubernetes集群时，阿里云容器服务提供两种网络插件：Terway和Flannel。

-   Flannel：使用的是简单稳定的社区的[Flannel](https://github.com/coreos/flannel)CNI插件，配合阿里云的VPC的高速网络，能给集群高性能和稳定的容器网络体验，但功能偏简单，支持的特性少。例如，不支持基于Kubernetes标准的Network Policy。
-   Terway：是阿里云容器服务Kubernetes版自研的网络插件，将阿里云的弹性网卡分配给容器，支持基于Kubernetes标准的Network Policy来定义容器间的访问策略，支持对单个容器做带宽的限流。对于不需要使用Network Policy的用户，可以选择Flannel，其他情况建议选择Terway。

    **说明：**

    -   Terway的Network Policy是通过集成Calico的Felix组件实现的，因此Network Policy的能力和Calico完全一致。对于最初为了使用Calico而自建集群的客户，目前可以通过Terway转换到容器服务Kubernetes版上来。
    -   Terway目前集成的Felix版本为3.5.2。

