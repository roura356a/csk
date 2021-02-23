# 配置安全沙箱节点和Docker节点的混合集群

节点池支持多种容器运行时，但一个节点池只能选一种容器运行时。您可以在一个集群创建不同容器运行时节点池，从而创建拥有不同容器运行时节点的混合集群。本文介绍如何在一个集群中混合配置安全沙箱节点池和Docker节点池。

已创建一个ACK集群，请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes托管版集群.md)。

**注意：** 创建的ACK集群需要符合以下条件。

-   Kubernetes版本大于等于1.14.6-aliyun.1。
-   网络插件必须为Flannel或Terway非独占弹性网卡模式。
-   存储插件不支持FlexVolume，只支持CSI-Plugin（版本大于或等于v1.14.8.39-0d749258-aliyun）。
-   logtail-ds版本大于或等于V0.16.34.2-f6647154-aliyun。

## 注意事项

-   默认情况下，每个集群中最多可包含100个节点。如果您需要添加更多节点，请提交工单申请，请参见[提交工单](https://workorder-intl.console.aliyun.com/console.htm)。
-   添加已有云服务器时，请确保您的云服务器有EIP（专有网络），或者相应VPC已配置NAT网关。同时您需要确保相应节点能正常访问公网，否则会导致添加云服务器失败。

## 创建Docker节点池

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面，选择目标集群，在该集群**操作**列下单击**节点池**。

4.  在**节点池**页面单击**创建节点池**，设置创建Docker运行时节点池的配置项。

    有关配置项的详细描述请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes托管版集群.md)。需要注意的配置项如下：

    |配置项|描述|
    |---|--|
    |**容器运行时**|选择**Docker**，该节点池下的所有节点的容器运行时都是Docker。|
    |**数量**|设置节点池初始节点数量。如不需要创建节点，可以设置为0。|
    |**操作系统**|选择节点的操作系统，支持CentOS、AliyunLinux、Windows操作系统。|

5.  单击**确定**。


## 创建安全沙箱节点池

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面，选择目标集群，在该集群**操作**列下单击**节点池**。

4.  在节点池页面单击**创建节点池**，设置创建安全沙箱运行时节点池的配置项。

    有关配置项的详细描述请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes托管版集群.md)。需要注意的配置项如下：

    |配置项|描述|
    |---|--|
    |**容器运行时**|选择**安全沙箱**，该节点池下的所有节点的容器运行时都是安全沙箱。|
    |**数量**|设置节点池初始节点数量。如不需要创建节点，可以设置为0。|
    |**付费类型**|安全沙箱运行时只能运行在神龙节点上，默认情况下神龙节点只支持包年包月付费类型。|
    |**挂载数据盘**|请至少挂载一块不小于200GiB的数据盘。|
    |**操作系统**|选择节点的操作系统，安全沙箱节点只支持AliyunLinux操作系统。|

5.  单击**确定**。


-   您可以在**节点池**页面查看节点池状态，节点池状态显示**已激活**，表明节点池创建成功。
-   您可以连接ACK集群，查看节点的详细信息。
    1.  在**节点池**页面，单击新建的节点池名称，进入节点池详情页。在**节点池信息**区域查找并记录**节点池ID**。

        ![节点ID](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0427204061/p127968.png)

    2.  连接Kubernetes集群，详情请参见[通过kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群管理/连接集群/通过kubectl连接Kubernetes集群.md)。
    3.  输入以下命令，获取节点名称。

        ```
        kubectl get node --show-labels | grep -E "${节点池ID}|${节点池ID}"
        ```

        ![节点ID](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6506659951/p127399.png)

    4.  输入以下命令，查看节点详情。

        ```
        kubectl get node -o wide | grep -E "${节点名}|${节点名}"
        ```

        ![结果验证](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6506659951/p127388.png)


