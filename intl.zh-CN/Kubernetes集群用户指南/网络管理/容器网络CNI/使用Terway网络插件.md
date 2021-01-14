---
keyword: [Terway, 网络规划, terway和flannel]
---

# 使用Terway网络插件

本文介绍如何使用阿里云容器服务Kubernetes版ACK集群的Terway网络插件。

## Terway网络插件

Terway网络插件是ACK自研的网络插件，将原生的弹性网卡分配给Pod实现Pod网络：

-   将阿里云的弹性网卡和辅助IP分配给容器。
-   支持基于Kubernetes标准的网络策略（Network Policy）来定义容器间的访问策略，并兼容Calico的网络策略。

![Terway网络插件](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4385659951/p32414.png)

在Terway网络插件中，每个Pod都拥有自己网络栈和IP地址。同一台ECS内的Pod之间通信，直接通过机器内部的转发，跨ECS的Pod通信，报文通过VPC的弹性网卡直接转发。由于不需要使用VxLAN等的隧道技术封装报文，因此具有较高的通信性能。

## Flannel与Terway

在创建集群时，ACK提供Terway和Flannel两种网络插件：

-   Terway：是阿里云容器服务ACK自研的网络插件。Terway将阿里云的弹性网卡分配给容器，支持基于Kubernetes标准的网络策略来定义容器间的访问策略，支持对单个容器做带宽的限流。Terway拥有更为灵活的IPAM（容器地址分配）策略，避免地址浪费。如果您不需要使用网络策略（Network Policy），可以选择Flannel，其他情况建议选择Terway。
-   Flannel：使用的是简单稳定的社区[Flannel](https://github.com/coreos/flannel) CNI网络策略（Network Policy）。配合阿里云的VPC的高速网络，能给集群高性能和稳定的容器网络体验。Flannel功能偏简单，支持的特性少。例如，不支持基于Kubernetes标准的网络策略（Network Policy）。

|对比项|Terway|Flannel|
|---|------|-------|
|性能|Pod地址即为VPC中地址，无NAT损耗支持独占ENI模式，几乎无损。|配合阿里云VPC路由，Pod地址为虚拟地址，存在NAT转换损耗。|
|安全|支持使用网络策略Network Policy。|不支持使用网络策略Network Policy。|
|地址管理|无需按节点分配地址段，随用随分配，地址无浪费。|节点维度划分地址段，大规模集群下地址浪费多。|
|SLB|SLB后端直接对接Pod，支持业务无中断升级。|SLB后端不能直接对接Pod，需要通过NodePort转发。|

## 网络规划

如果使用Terway网络插件，您需要在一个VPC网络下创建2个虚拟交换机，这2个虚拟交换机需要在同一个可用区内。

**说明：**

-   虚拟交换机网段，对应节点所使用的网段。
-   Pod虚拟交换机网段，对应Pod所使用的网段。

您可以使用以下网络规划配置已快速搭建Terway网络。

|专有网络网段|虚拟交换机|Pod虚拟交换机|Service CIDR|
|------|-----|--------|------------|
|192.168.0.0/16|192.168.0.0/19|192.168.32.0/19|172.21.0.0/20|

更多信息，请参见[Kubernetes集群网络规划](/intl.zh-CN/Kubernetes集群用户指南/网络管理/Kubernetes集群网络规划.md)。

## 注意事项

-   如果要使用Terway插件，建议您选择较高规格和较新类型的ECS机型，即5代或者6代的8核以上机型。更多信息，请参见[实例规格族](/intl.zh-CN/实例/实例规格族.md)。
-   单节点所支持的最大Pod数取决于该节点的弹性网卡（ENI）数。

    -   共享ENI支持的最大Pod数=（ECS支持的ENI数-1）×单个ENI支持的私有IP数
    -   独占ENI支持的最大Pod数 = ECS支持的ENI数－1
    **说明：** 您在创建集群或扩容集群的**实例规格**区域，可以查看ECS支持的ENI数量。关于创建集群的具体步骤，请参见[创建Kubernetes Pro版集群](/intl.zh-CN/Kubernetes集群用户指南/ACK Pro集群/创建Kubernetes Pro版集群.md)。


## 为Terway网络插件配置集群网络

为Terway网络插件配置集群网络的关键参数配置说明如下：

**说明：** 本文以创建标准托管版集群为例，说明如何为Terway网络插件配置集群网络。关于创建集群的具体步骤，请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes托管版集群.md)。

-   **专有网络**：选择专有网络。如果您未创建专有网络，请按照以下步骤创建专有网络VPC。
    1.  单击**创建专有网络**。

        ![VPC](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0840730161/p212505.png)

    2.  在创建专有网络页面，设置**名称**为vpc\_192\_168\_0\_0\_16，在**请输入网段**文本框中，输入192.168.0.0/16。
    3.  在**创建交换机**区域，设置虚拟交换机名称为**node\_switch\_192\_168\_0\_0\_19**、可用区、和IPv4网络为**192.168.0.0/19**，单击**+添加**创建第二个虚拟交换机。

        ![vSwitch](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0032730161/p212508.png)

        **说明：** 请确保两个交换机在同一个可用区。

    4.  在第二个**创建交换机**区域，配置Pod虚拟交换机，设置名称为**pod\_switch\_192\_168\_32\_0\_19**，IPv4网段为**192.168.32.0/19**。
-   **虚拟交换机**：选择自规划的网络或者[创建专有网络](#li_fm5_d5a_ujk)步骤中创建的虚拟交换机。
-   **网络插件**：选择**Terway**。

    设置**网络插件**为**Terway**时，需要配置**Terway模式**：

    -   设置是否选中**Pod独占弹性网卡以获得最佳性能**

-   如果选中，Pod将独占一个专有的弹性网卡。
-   如果不选中，使用弹性网卡的辅助IP分配给Pod，一个Pod占用一个弹性网卡辅助IP地址。
    -   设置是否选中**IPvlan**

-   只在弹性网卡共享模式支持选中。
-   如果选中，采用IPVLAN eBPF作为网卡共享模式虚拟化技术，并且只能使用Alibaba Cloud Linux 2系统，性能优于默认模式。
-   如果不选中，则使用默认模式，采用策略路由作为网卡共享模式虚拟化技术，同时兼容Centos 7和Alibaba Cloud Linux 2的系统。
**说明：** 当前只有白名单用户可使用上述**Pod独占弹性网卡以获得最佳性能**和**IPvlan**两种功能。[提交工单](https://workorder-intl.console.aliyun.com/console.htm)申请使用。

    -   设置是否选中**NetworkPolicy支持**

-   只在弹性网卡共享模式下支持选中，默认不选中。
-   如果选中，集群支持使用Kubernetes的NetworkPolicy策略对Pod进行网络控制。
-   如果不选中，集群将不会支持使用Kubernetes的NetworkPolicy策略对Pod进行网络控制，这样将不存在网络策略对Kubernetes的API Server产生过大的负载。
-   **Pod虚拟交换机**：选择自规划的网络或者[创建专有网络](#li_fm5_d5a_ujk)步骤中创建的Pod虚拟交换机。
-   **Service CIDR**：保留默认值。

