# 下线Swarm集群 {#concept_371488 .concept}

## 确认Swarm集群流量 {#section_5i9_xfw_e1x .section}

1.  确认SLB流量。

    在下线Swarm集群前，需要重点确认，老的Swarm SLB是否还有流量进来，可以通过SLB提供的监控视图查看，其支持按SLB实例或监听规则视角查看，您可以通过如下操作进行查看。

    1.  登录[负载均衡管理控制台](https://slbnext.console.aliyun.com/)，在实例管理页面，单击目标实例，进入实例详情页面。
    2.  在**监控**页签，设置监听规则等，可以看到监控视图。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/301604/155954570948153_zh-CN.png)

2.  确认应用流量。

    **建议通过客户自己的业务监控体系重点观察一段时间**，确认老的Swarm集群是否还有业务流量进来；避免出现客户端升级未完成或DNS有缓存，导致有残余流量存在，进而影响线上业务。


## 下线Swarm集群 {#section_x9r_siy_0u9 .section}

在确认K8S集群正常对外提供服务，老的Swarm集群没有残余流量后，我们可以开始着手准备下线老Swarm集群；主要包括以下3个步骤：

1.  备份 Swarm 集群配置。

    为了确保 K8S 集群运行中出现未知问题时，能快速回溯原 Swarm集群配置，**建议备份一份老Swarm 集群的配置，避免配置丢失不可恢复**，包括但不仅限于以下维度的配置：

    -   集群 Node 标签、网络、数据卷配置、配置项、SLB 配置等。
    -   各应用编排文件、日志等。
2.  下线Swarm集群资源。

    **说明：** 在Swarm集群中，Node节点ECS、应用、服务、SLB等云产品都挂在Swarm集群上，集群被删除，其关联的云产品都会被删除，请慎重确认。

    登录[容器服务管理控制台](https://cs.console.aliyun.com)，在左侧导航栏选择**集群** \> **集群**，在目标集群右侧单击**删除**，[删除集群](../../../../intl.zh-CN/用户指南/Kubernetes集群/集群管理/删除集群.md#)。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/301604/155954570948154_zh-CN.png)

3.  下线云盘资源。

    云盘只能挂载在单一ECS机器上，所以在[数据卷迁移](intl.zh-CN/最佳实践/Swarm迁移Kubernetes/迁移集群配置.md#section_q48_bz4_062)章节我们强调过，如果云盘是作为数据盘需要迁移的，则只能选择停机迁移到K8S集群。

    如果云盘只是作为数据盘用于数据收集且无须带数据迁移的，则在K8S集群里面可以新建一个云盘作为数据存储；而在老的Swarm集群下线之后，其对应挂载的云盘并不会自动释放，只会变成**待挂载**状态。

    **说明：** 请按业务需要先收集或备份好云盘上的数据再释放云盘，避免数据丢失不可恢复。

    若需释放Swarm集群云盘，则需到云盘控制台手动释放。

    1.  登录[ESC管理控制台](https://ecs.console.aliyun.com/)，在左侧导航栏选择**存储** \> **云盘**。
    2.  在磁盘列表页面，在目标云盘右侧选择**更多** \> **释放**，完成Swarm集群云盘的释放。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/301604/155954570948155_zh-CN.png)


