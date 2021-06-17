---
keyword: [自定义kubelet, 托管节点池, kube-reserved, system-reserved, kube-api-qps]
---

# 自定义托管节点池的kubelet参数

为了方便您对节点的kubelet参数进行自定义配置，容器服务ACK提供了节点池维度的kubelet自定义功能。例如，使用kube-reserved和system-reserved预留资源以避免节点资源耗尽；当Pod规模较大时调整kube-api-qps以提高kubelet的并发处理能力。本文介绍如何自定义托管节点池的kubelet参数。

-   已创建一个ACK Pro托管版集群且集群版本大于等于1.13（目前只支持Pro版本集群）。具体操作，请参见[创建ACK Pro版集群](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/创建ACK Pro版集群.md)。
-   已创建一个托管节点池。具体操作，请参见[创建托管节点池](/cn.zh-CN/Kubernetes集群用户指南/节点与节点池/托管节点池/管理托管节点池.md)。

kubelet是Kubernetes中的核心组件，通过二进制运行在每一个集群节点上的代理程序，也是Kubernetes集群启动的第一个服务。

您可以调整kubelet参数来满足自己的需求。提交配置参数后，节点池内的节点会分批次变更至指定配置，新扩容的节点也会自动应用该配置。

## 注意事项

-   节点池和kubelet参数的变更依赖阿里云自定义的资源**NodePool**和kube-system下的所有配置项（ConfigMap）资源，为了保证节点池的稳定运行，请不要擅自修改这些资源。
-   kubelet参数变更会重启kubelet进程，重启期间节点的**ready**状态、事件（Events）等会有变化，如果业务或监控方强依赖这些状态，请谨慎使用该功能。
-   单节点的变更一般一分钟内可以完成。变更期间不要擅自操作节点，节点变更时异常操作会终止流程，并尝试回滚异常节点。变更或回滚失败可以先自行排查kubelet日志，或[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)给阿里云研发团队。
-   配置kubelet参数前请仔细查看对应文档、了解各参数含义，避免配置错误。关于完整的参数列表和属性，请参见[kubelet](https://github.com/kubernetes/kubernetes/blob/master/staging/src/k8s.io/kubelet/config/v1beta1/types.go)。

## 自定义托管节点池的kubelet参数

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**节点管理** \> **节点池**。

5.  在节点池页面的目标托管节点池右侧**操作**列下，单击**Kubelet**配置。

6.  在kubelet配置弹框中，完成相关参数的设置，然后单击**确定**。

    已开放配置的kubelet参数分为两类：

    -   qps和burst类：主要为kubelet中的限流策略相关的参数。
    -   资源配置及驱逐类：主要为kubelet各项资源预留和配置相关的参数，以及节点驱逐配置相关的参数。
    |参数分类|参数名|类型校验|示例|参数作用|
    |----|---|----|--|----|
    |qps和burst类|event-qps|int|5|    -   设置大于0的值表示限制每秒可生成的事件数量。
    -   设置为0表示不限制。
默认值为5。 |
    |event-burst|int|10|上报事件的个数上限，在遵从event-qps阈值约束的前提下临时允许事件记录达到此数目。仅在`--event-qps`大于0时使用。默认值为10。 |
    |kube-api-qps|int|5|与apiserver组件通信的每秒查询数（QPS） 值。默认值为5。 |
    |kube-api-burst|int|10|每秒发送到apiserver组件的请求数量上限。默认值为10。 |
    |registry-qps|int|5|    -   如果`--registry-qps`大于 0，用来限制镜像仓库的QPS上限。
    -   设置为0，表示镜像仓库的QPS不受限制。
默认值为5。 |
    |registry-burst|int|10|设置突发性镜像拉取的个数上限，在不超过registry-qps设置值的前提下暂时允许此参数所给的镜像拉取个数。仅在 `--registry-qps`大于0时使用。默认值为10。 |
    |serialize-image-pulls|bool|true|逐一拉取镜像。默认值为true。|
    |资源配置及驱逐类|eviction-hard|map\[String\]String|memory.available: 300Minodefs.available: 10%nodefs.inodesFree: 5%imagefs.available: 15%imagefs.inodesFree: 5%pid.available: 100|触发Pod驱逐操作的一组硬性限制。ACK集群设置的默认值为：`--eviction-hard=imagefs.available<15%,memory.available<300Mi,nodefs.available<10%,nodefs.inodesFree<5%`|
    |eviction-soft|map\[String\]String|同eviction-hard|触发Pod驱逐操作的一组软性限制。ACK集群无默认配置。 |
    |kube-reserved|map\[String\]String|cpu:100mmemory: 300Miephemeral-storage: 1Gipid: 10000|Kubernetes系统预留的资源配置。ACK集群设置的默认值为：`--system-reserved=memory=300Mi-system-reserved=pid=10000`|
    |system-reserved|map\[String\]String|同kube-reserved|系统预留的资源配置。ACK集群设置的默认值为：`--kube-reserved=memory=400Mi--kube-reserved=pid=10000`|

    节点池内所有节点的配置全部完成更新后，节点池状态显示**已激活**。

    资源预留类参数可以通过执行describe node来验证变更，而qps和burst类参数只能通过压测或执行命令（例如`journalctl -u kubelet`）查看kubelet日志进行确认。


## kubelet配置的更新策略

按节点池内节点数量的10%为一批，逐批次对节点池内的节点进行变更。如果有失败的节点则终止变更流程，且将失败节点回滚至上一次稳定配置。

例如：节点池内一共5个节点（按节点池内节点数量的10%为一批，并向上取整数），会分为5批，每批1个逐台进行变更。如果1个节点变更失败，则终止流程，并回滚失败的节点，在此期间节点池状态处于**更新中**，您需要排查失败原因后再继续操作。如果节点池内共100个节点，则每批10个，其他流程不变。

