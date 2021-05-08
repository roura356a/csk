---
keyword: [VPC附加网段, 虚拟交换机vswitch, k8s集群]
---

# 使用VPC附加网段扩充集群的虚拟交换机

在创建容器服务ACK的集群时需要为其选择VPC，后续对集群的扩容只能在集群所属的VPC中进行。如果该VPC的网段之前被规划得太小没有足够IP供扩容时，您可以采用VPC附加网段的方式对VPC进行扩容来满足业务扩容需求。本文介绍如何使用VPC附加网段扩充集群的虚拟交换机（VSwitch）。

-   集群所属的VPC现有网段无法分配足够的IP给业务扩容。
-   已创建ACK专有版集群或者于2021年2月及之后创建ACK托管版集群。具体操作，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)或[创建Kubernetes专有版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes专有版集群.md)。

    **说明：** 2021年2月之前创建的ACK托管版集群不支持VPC附加网段扩容。针对这种情况，您可以升级集群至ACK Pro托管版集群后再使用VPC附加网段扩容集群的虚拟交换机。关于如何升级标准托管版集群至Pro托管版集群，请参见[热迁移ACK标准托管集群至Pro托管集群](/cn.zh-CN/Kubernetes集群用户指南/集群/热迁移ACK标准托管集群至Pro托管集群.md)。


## 操作步骤

1.  选择可以扩容的网段。

    1.  统计现在使用的网段。

        包括但不限于以下网段类型：

        -   现有的VPC网段。

            关于如何查看现有的VPC网段的具体步骤，请参见[查看专有网络](/cn.zh-CN/专有网络和交换机/使用专有网络.md)。

            ![vpc网段](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8361730261/p271329.png)

        -   该VPC中集群的Pod网段和Service网段。

            关于如何查看Pod网段及Service网段，请参见[查看基本信息](/cn.zh-CN/Kubernetes集群用户指南/集群/查看集群信息.md)。

            **说明：**

            -   如果集群使用的模式网络为Terway，查看Service网段。
            -   如果集群使用的模式网络为Flannel，查看Pod网段和Service网段。
        -   与该VPC打通的专线、VPN、CEN等外部网段。
    2.  选择一个与上述网段都不重叠的网段作为VPC附加网段。

        例如：在Flannel网络下，集群使用的网段如下：

        -   VPC网段为192.168.0.0/16。
        -   Pod网段为172.20.0.0/16。
        -   Service网段为172.21.0.0/16。
        -   VPC没有加入到专线、VPN、CEN等外部网络中。
        则，您可以选择附加网段为10.0.0.0/8。

2.  在VPC控制台上创建附加网段和对应的虚拟交换机。

    1.  登录[专有网络管理控制台](https://vpcnext.console.aliyun.com/vpc)。

    2.  在顶部菜单栏处，选择专有网络的地域。

    3.  在**专有网络**页面，找到目标专有网络，单击专有网络的ID。

    4.  在**专有网络基本信息**页面，单击**网段管理**页签，然后单击**添加附加IPv4网段**，然后添加上一步中找到的合适的IPv4网段。

    5.  按照业务对可用区的需求，您可在这个附加IPv4网段下创建对应可用区的虚拟交换机。

        关于如何创建虚拟交换机的具体步骤，请参见[创建交换机](/cn.zh-CN/专有网络和交换机/使用交换机.md)。

3.  在集群的安全组中放开对上述选择的附加网段的出、入方向的所有协议允许规则。

    关于如何添加安全组规则的具体步骤，请参见[添加安全组规则](/cn.zh-CN/安全/安全组/添加安全组规则.md)。

4.  在集群扩容界面选择新的虚拟交换机用于扩容节点。

    在上述创建的附加网段中的虚拟交换机下即可扩容新的网段的节点。具体步骤，请参见[扩容集群](/cn.zh-CN/Kubernetes集群用户指南/集群/扩容集群.md)。

    **说明：** 在Terway网络模式下，您可以修改Terway的交换机配置用于扩容Pod。关于如何把创建的附加网段中的虚拟交换机用于Pod网络，请参见[扩容Terway场景下的虚拟交换机](/cn.zh-CN/Kubernetes集群用户指南/网络/容器网络CNI/扩容Terway场景下的虚拟交换机VSwitch.md)。

    扩容完成后：

    -   ACK专有版的管控节点与附加网段的节点可以直接通信，无需做额外配置。
    -   ACK标准版和Pro托管版集群需要对管控面做重新配置，否则新的节点上的Pod无法通过API Server执行kubectl exec和kubectl logs命令。您需要[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)联系技术人员重新配置管控面。
5.  验证添加的VPC附加网段。

    方式如下：

    -   新分配的节点或者Pod使用了新添加的VPC附加网段中的IP地址。
    -   新分配的节点状态为**Ready**状态。
    -   在新的节点和Pod中分别测试与集群中原有节点和Pod的网络的联通性。

**相关文档**  


[Kubernetes集群网络规划](/cn.zh-CN/Kubernetes集群用户指南/网络/Kubernetes集群网络规划.md)

[添加附加IPv4网段](/cn.zh-CN/专有网络和交换机/使用专有网络.md)

[扩容Terway场景下的虚拟交换机](/cn.zh-CN/Kubernetes集群用户指南/网络/容器网络CNI/扩容Terway场景下的虚拟交换机VSwitch.md)

[热迁移ACK标准托管集群至Pro托管集群](/cn.zh-CN/Kubernetes集群用户指南/集群/热迁移ACK标准托管集群至Pro托管集群.md)

