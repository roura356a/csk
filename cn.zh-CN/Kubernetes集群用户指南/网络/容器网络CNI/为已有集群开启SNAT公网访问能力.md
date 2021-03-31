---
keyword: [SNAT, 公网访问, 集群]
---

# 为已有集群开启SNAT公网访问能力

如果您在创建集群时未开启SNAT功能，可以通过ACK控制台为已有集群开启SNAT功能。ACK暂不支持为已有集群通过API方式开启SNAT功能。本文介绍如何在ACK控制台为未开启公网访问的集群开启SNAT，以实现集群的公网访问能力。

SNAT可以为VPC内无公网IP的ECS实例或集群提供访问互联网的代理服务。有关于SNAT相关的说明，请参见[什么是NAT网关](/cn.zh-CN/产品简介/什么是NAT网关.md)。

## 操作步骤

为已有集群开启SNAT公网访问能力的流程图及对应操作步骤如下。

![创建SNAT](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9263117161/p259133.png)

1.  创建NAT网关。

    1.  登录[NAT网关管理控制台](https://vpc.console.aliyun.com/nat)。

    2.  在左侧导航栏，单击**NAT网关**。

    3.  在**NAT网关**页面，单击**创建NAT网关**。

        创建NAT网关的参数说明，请参见[创建NAT网关]()。

        **说明：** 创建NAT网关时，地域和VPC的配置需要和集群的保持一致。

2.  创建弹性公网IP。

    在左侧导航栏，选择**弹性公网IP** \> **弹性公网IP**，然后在**弹性公网IP**页面，单击**购买弹性公网IP**。

    如果您已经有弹性公网IP的话，可以跳过这一步。

3.  绑定弹性公网IP到对应的NAT网关。

    1.  在**NAT网关**页面，选择目标NAT网关，然后在其右侧选择**![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4475659951/p140355.png)** \> **绑定弹性公网IP**。

    2.  在**绑定弹性公网IP**面板中，在**从已有弹性公网IP中选择**下拉框中，选择创建的弹性公网IP。

    3.  单击**确定**。

4.  为NAT网关创建SNAT条目。

    1.  在**NAT网关**页面，选择目标NAT网关，然后在其右侧，单击**管理**。

    2.  单击**SNAT管理**页签，然后单击**创建SNAT条目**。

    3.  在**创建SNAT条目**页面，配置以下参数，然后单击**确定创建**。

        关于更多创建SNAT条目的参数配置说明，请参见[创建SNAT条目](/cn.zh-CN/基本功能操作/创建SNAT实现访问公网服务.md)。

        |配置|说明|
        |--|--|
        |**SNAT条目粒度**|选中**交换机粒度**，然后选择相应的集群网络所使用的虚拟交换机。        -   Terway网络：需要选中节点虚拟交换机和Pod虚拟交换机所对应ID。
        -   Flannel网络：只需选中节点虚拟交换机所对应的ID。
执行以下步骤，查询集群网络所使用虚拟交换机ID。

        1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
        2.  在左侧导航栏，单击**集群**，然后在集群列表中单击目标集群名称。
        3.  在集群信息页面，单击**集群资源**页签，然后查看虚拟交换机ID。

![Flannel网络](../images/p255169.png "Flannel网络")

![Terway网络](../images/p255209.png "Terway网络") |
        |**选择公网IP地址**|选择用来提供互联网访问的公网IP。|

    创建SNAT条目并配置SNAT规则之后，集群就开启了SNAT。您可以在[NAT网关管理控制台](https://vpc.console.aliyun.com/nat)看到NAT网关的详细信息，包括SNAT使用的弹性公网IP。下图为Terway网络模式下开启了SNAT公网访问的NAT网关实例。

    ![NAT](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0787117161/p140370.png)

    单击NAT网关名称，在对应NAT网关实例的**SNAT管理**页签中，查看并确认已绑定了正确的集群所用虚拟交换机。下图为Terway网络模式下创建的SNAT条目信息。

    ![snat](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1687517161/p259134.png)


登录到集群的节点上，然后访问公网，查看网络正常，数据包无丢失。

![SNAT 2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4475659951/p140471.png)

