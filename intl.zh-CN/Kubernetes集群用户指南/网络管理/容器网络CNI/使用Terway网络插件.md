---
keyword: [Terway, 网络规划, terway和flannel]
---

# 使用Terway网络插件

Terway是阿里云开源的基于专有网络VPC的容器网络接口CNI（Container Network Interface）插件，支持基于Kubernetes标准的网络策略来定义容器间的访问策略。您可以通过使用Terway网络插件实现Kubernetes集群内部的网络互通。本文介绍如何使用阿里云容器服务Kubernetes版ACK集群的Terway网络插件。

Terway网络插件是ACK自研的网络插件，将原生的弹性网卡分配给Pod实现Pod网络，支持基于Kubernetes标准的网络策略（Network Policy）来定义容器间的访问策略，并兼容Calico的网络策略。

在Terway网络插件中，每个Pod都拥有自己网络栈和IP地址。同一台ECS内的Pod之间通信，直接通过机器内部的转发，跨ECS的Pod通信、报文通过VPC的弹性网卡直接转发。由于不需要使用VxLAN等的隧道技术封装报文，因此Terway模式网络具有较高的通信性能。

![Terway网络插件](../images/p32414.png "Terway网络模式图")

## Flannel与Terway对比

在创建集群时，ACK提供Terway和Flannel两种网络插件：

-   Terway：是阿里云容器服务ACK自研的网络插件。Terway将阿里云的弹性网卡分配给容器，支持基于Kubernetes标准的网络策略来定义容器间的访问策略，支持对单个容器做带宽的限流。Terway拥有更为灵活的IPAM（容器地址分配）策略，避免地址浪费。如果您不需要使用网络策略（Network Policy），可以选择Flannel，其他情况建议选择Terway。
-   Flannel：使用的是简单稳定的社区Flannel CNI插件。配合阿里云的VPC的高速网络，能给集群高性能和稳定的容器网络体验。Flannel功能偏简单，支持的特性少。例如，不支持基于Kubernetes标准的网络策略（Network Policy）。更多信息，请参见[Flannel](https://github.com/coreos/flannel)。

|对比项|Terway|Flannel|
|---|------|-------|
|性能|Pod地址即为VPC中地址，无NAT损耗支持独占ENI模式，几乎无损。|配合阿里云VPC路由，Pod地址为虚拟地址，存在NAT转换损耗。|
|安全|支持使用网络策略Network Policy。|不支持使用网络策略Network Policy。|
|地址管理|无需按节点分配地址段，随用随分配，地址无浪费。|节点维度划分地址段，大规模集群下地址浪费多。|
|SLB|SLB后端直接对接Pod，支持业务无中断升级。|SLB后端不能直接对接Pod，需要通过NodePort转发。|

## 注意事项

-   如果要使用Terway插件，建议您选择较高规格和较新类型的ECS机型，即5代或者6代的8核以上机型。更多信息，请参见[实例规格族](/intl.zh-CN/实例/实例规格族.md)。
-   单节点所支持的最大Pod数取决于该节点的弹性网卡（ENI）数。

    -   共享ENI支持的最大Pod数=（ECS支持的ENI数-1）×单个ENI支持的私有IP数
    -   独占ENI支持的最大Pod数=ECS支持的ENI数－1
    **说明：** 您在创建集群或扩容集群的**实例规格**区域，可以查看ECS支持的ENI数量。关于创建集群的具体步骤，请参见[创建Kubernetes Pro版集群](/intl.zh-CN/Kubernetes集群用户指南/ACK Pro集群/创建Kubernetes Pro版集群.md)。


## 步骤一：规划和准备集群网络

在创建ACK Kubernetes集群时，您需要指定专有网络VPC、虚拟交换机、 Pod网络CIDR（地址段）和Service CIDR（地址段）。如果使用Terway网络插件，您需要先创建在一个专有网络VPC，然后在VPC下创建两个虚拟交换机。这两个虚拟交换机需要在同一个可用区内。关于使用Terway网络模式时规划配置网段需要注意的更多信息，请参见[Kubernetes集群网络规划](/intl.zh-CN/Kubernetes集群用户指南/网络管理/Kubernetes集群网络规划.md)。

您可以使用以下网段配置，以快速搭建Terway网络。

|专有网络网段|虚拟交换机|Pod虚拟交换机|Service CIDR|
|------|-----|--------|------------|
|192.168.0.0/16|192.168.0.0/19|192.168.32.0/19|172.21.0.0/20|

**说明：**

-   虚拟交换机网段，对应节点所使用的网段。
-   Pod虚拟交换机网段，对应Pod所使用的网段。

本文使用上述推荐网段，说明如何创建一个专有网络和两个虚拟交换机。

1.  登录[专有网络管理控制台](https://vpcnext.console.aliyun.com/vpc)。

2.  在顶部菜单栏处，选择专有网络的地域，然后单击**创建专有网络**。

    **说明：** 专有网络的地域和要部署的云资源的地域必须相同。

3.  在创建专有网络页面，设置**名称**为vpc\_192\_168\_0\_0\_16，在**请输入网段**文本框中，输入192.168.0.0/16。

4.  在**创建交换机**区域，设置虚拟交换机名称为**node\_switch\_192\_168\_0\_0\_19**、可用区、和IPv4网络为**192.168.0.0/19**，单击**+添加**创建第二个虚拟交换机。

    ![vSwitch](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0032730161/p212508.png)

    **说明：** 请确保两个交换机在同一个可用区。

5.  在第二个**创建交换机**区域，配置Pod虚拟交换机，设置名称为**pod\_switch\_192\_168\_32\_0\_19**，IPv4网段为**192.168.32.0/19**。

6.  单击**确定**。


## 步骤二：配置Terway网络

为Terway网络插件配置集群网络的关键参数配置说明如下：

![terway](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0533730161/p212509.png)

**说明：** 本文以创建标准托管版集群为例，介绍为Terway网络插件配置集群网络涉及的参数。关于创建集群的具体步骤，请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes托管版集群.md)。

-   **专有网络**：选择[步骤一：规划和准备集群网络](#section_2nd_fho_f25)中创建的专有网络。
-   **虚拟交换机**：选择[步骤一：规划和准备集群网络](#section_2nd_fho_f25)中创建的虚拟交换机。
-   **网络插件**：选择**Terway**。

    设置**网络插件**为**Terway**时，需要配置**Terway模式**：

    -   设置是否选中**Pod独占弹性网卡以获得最佳性能**

-   如果选中，Pod将独占一个专有的弹性网卡。
-   如果不选中，使用弹性网卡的辅助IP分配给Pod，一个Pod占用一个弹性网卡辅助IP地址。
    -   设置是否选中**IPvlan**

-   只在弹性网卡共享模式支持选中。
-   如果选中，采用IPVLAN eBPF作为网卡共享模式虚拟化技术，并且只能使用Alibaba Cloud Linux 2系统，性能优于默认模式。
-   如果不选中，则使用默认模式，采用策略路由作为网卡共享模式虚拟化技术，同时兼容Centos 7和Alibaba Cloud Linux 2的系统。
**说明：** 当前只有白名单用户可使用上述**Pod独占弹性网卡以获得最佳性能**功能。[提交工单](https://workorder-intl.console.aliyun.com/console.htm)申请使用。

    -   设置是否选中**NetworkPolicy支持**

-   只在弹性网卡共享模式下支持选中，默认不选中。
-   如果选中，集群支持使用Kubernetes的NetworkPolicy策略对Pod进行网络控制。
-   如果不选中，集群将不会支持使用Kubernetes的NetworkPolicy策略对Pod进行网络控制，这样将不存在网络策略对Kubernetes的API Server产生过大的负载。
-   **Pod虚拟交换机**：选择[步骤一：规划和准备集群网络](#section_2nd_fho_f25)中创建的Pod虚拟交换机。
-   **Service CIDR**：保留默认值。

**相关文档**  


[网络概述](/intl.zh-CN/Kubernetes集群用户指南/网络管理/网络概述.md)

[Kubernetes集群网络规划](/intl.zh-CN/Kubernetes集群用户指南/网络管理/Kubernetes集群网络规划.md)

[扩容Terway场景下的虚拟交换机VSwitch](/intl.zh-CN/Kubernetes集群用户指南/网络管理/容器网络CNI/扩容Terway场景下的虚拟交换机VSwitch.md)

