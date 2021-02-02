# API概览

容器服务Kubernetes版提供以下API接口。

**说明：** 在使用API管理Kubernetes集群前，请确保您已阅读和同意[阿里云容器服务Kubernetes版免责声明](/intl.zh-CN/相关协议/阿里云容器服务Kubernetes版免责声明.md)。

## 集群

|API|描述|
|---|--|
|[创建Kubernetes专有版集群](/intl.zh-CN/API参考/集群/创建集群/创建Kubernetes专有版集群.md)|调用CreateCluster创建一个标准Kubernetes专有版集群。|
|[创建Kubernetes托管版集群](/intl.zh-CN/API参考/集群/创建集群/创建Kubernetes托管版集群.md)

|调用CreateCluster创建一个新的Managed Kubernetes集群实例，并创建指定数量的节点。|
|[创建Serverless Kubernetes集群](/intl.zh-CN/API参考/集群/创建集群/创建Serverless Kubernetes集群.md)

|调用CreateCluster创建一个新的Serverless Kubernetes集群实例。|
|[创建Kubernetes边缘托管版集群](/intl.zh-CN/API参考/集群/创建集群/创建Kubernetes边缘托管版集群.md)

|调用CreateCluster创建一个新的Kubernetes边缘托管版集群实例。|
|[创建专有版安全沙箱Kubernetes集群](/intl.zh-CN/API参考/集群/创建集群/创建专有版安全沙箱Kubernetes集群.md)

|调用CreateCluster创建一个专有版安全沙箱Kubernetes集群。|
|[创建标准托管版安全沙箱Kubernetes集群](/intl.zh-CN/API参考/集群/创建集群/创建标准托管版安全沙箱Kubernetes集群.md)|调用CreateCluster创建一个托管版安全沙箱Kubernetes集群。|
|[扩容集群](/intl.zh-CN/API参考/集群/扩容集群/扩容集群.md)

|调用ScaleOutCluster增加集群中Worker节点的数量（支持多可用区实例）。|
|[扩容安全沙箱容器集群](/intl.zh-CN/API参考/集群/扩容集群/扩容安全沙箱容器集群.md)

|调用ScaleOutCluster增加安全沙箱容器集群中Worker节点的数量（支持多可用区实例）。|
|[扩容Kubernetes边缘托管版集群](/intl.zh-CN/API参考/集群/扩容集群/扩容Kubernetes边缘托管版集群.md)

|调用ScaleOutCluster增加集群中Worker节点数量。目前只能扩容边缘节点服务ENS（Edge Node Service）实例。|
|[查询集群信息](/intl.zh-CN/API参考/集群/查询集群信息.md)

|调用DescribeClusterDetail，根据集群ID，查看集群的详细信息。|
|[查询所有集群列表](/intl.zh-CN/API参考/集群/查询所有集群列表.md)

|调用DescribeClustersV1查看您在ACK中创建的所有集群（包括Swarm和Kubernetes集群）。|
|[查询集群资源](/intl.zh-CN/API参考/集群/查询集群资源.md)

|调用DescribeClusterResources查询指定集群的所有资源。|
|[查询集群代理](/intl.zh-CN/API参考/集群/查询集群代理.md)

|调用DescribeExternalAgent查询额外代理。|
|[查询用户配额](/intl.zh-CN/API参考/集群/查询用户配额.md)

|调用DescribeUserQuota查询用户配额。|
|[查询集群日志](/intl.zh-CN/API参考/集群/查询集群日志.md)

|调用DescribeClusterLogs查询指定集群日志。|
|[获取集群kubeconfig接口](/intl.zh-CN/API参考/集群/获取集群kubeconfig接口.md)

|调用DescribeClusterUserKubeconfig返回包含当前登录用户身份信息的Kubernetes集群访问kubeconfig。|
|[修改集群](/intl.zh-CN/API参考/集群/修改集群.md)

|调用ModifyCluster修改集群。|
|[删除集群](/intl.zh-CN/API参考/集群/删除集群.md)

|调用DeleteCluster根据集群ID，删除集群实例，并释放集群所有节点资源。|
|[修改集群标签](/intl.zh-CN/API参考/标签/修改集群标签.md)

|调用ModifyClusterTags修改当前Kubernetes集群的tag接口。|
|[查询资源标签列表](/intl.zh-CN/API参考/标签/查询资源标签列表.md)

|调用ListTagResources，查询可见的资源标签关系。|
|[查询Kubernetes版本详情](/intl.zh-CN/API参考/集群/查询Kubernetes版本详情.md)|调用DescribeKubernetesVersionMetadata查询支持的Kubernetes版本的详细信息。|
|[查询任务详情](/intl.zh-CN/API参考/集群/查询任务详情.md)|调用DescribeTaskInfo根据任务ID查询任务执行详情。|

## 权限

|API|描述|
|---|--|
|[查询RAM用户集群授权信息]()

|调用DescribeUserPermission查询指定RAM用户的集群授权信息。|
|[全量更新RAM用户集群授权信息]()

|调用GrantPermissions全量更新RAM用户集群授权信息。|

## 节点

|API|描述|
|---|--|
|[查询集群节点](/intl.zh-CN/API参考/节点/查询集群节点.md)

|调用DescribeClusterNodes查询集群节点。|
|[移除集群节点](/intl.zh-CN/API参考/节点/移除集群节点.md)

|调用RemoveClusterNodes移除指定集群额外节点。|
|[添加已有实例到集群](/intl.zh-CN/API参考/节点/添加已有实例到集群.md)

|调用AttachInstances添加已有实例到集群。|
|[手动添加已有实例至集群](/intl.zh-CN/API参考/节点/手动添加已有实例至集群.md)

|调用DescribeClusterAttachScripts添加已有节点到边缘Kubernetes托管集群。该API返回唯一的可执行脚本，您获取脚本后，在已有节点上执行即可完成该节点的接入。|
|[添加已有ENS节点至Kubernetes边缘托管集群]()

|调用AttachInstances接口添加已有ENS节点至边缘托管集群。|

## 节点池

|API|描述|
|---|--|
|[创建节点池](/intl.zh-CN/API参考/节点/节点池/创建节点池.md)

|调用CreateClusterNodePool为集群创建节点池。|
|[扩容节点池](/intl.zh-CN/API参考/节点/节点池/扩容节点池.md)

|调用ScaleClusterNodePool扩容节点池节点。|
|[修改节点池配置](/intl.zh-CN/API参考/节点/节点池/修改节点池配置.md)

|调用ModifyClusterNodePool更新节点配置。|
|[查询节点池列表](/intl.zh-CN/API参考/节点/节点池/查询节点池列表.md)

|调用DescribeClusterNodePools查询集群内所有节点池详情。|
|[查询节点池详情](/intl.zh-CN/API参考/节点/节点池/查询节点池详情.md)

|调用DescribeClusterNodePoolDetail查询集群指定节点池详情。|
|[删除节点池](/intl.zh-CN/API参考/节点/节点池/删除节点池.md)|调用DeleteClusterNodepool删除节点池。|

## 升级

|API|描述|
|---|--|
|[查询集群升级状态](/intl.zh-CN/API参考/升级/查询集群升级状态.md)

|调用GetUpgradeStatus查询集群升级状态。|
|[升级集群](/intl.zh-CN/API参考/升级/升级集群.md)

|调用UpgradeCluster升级指定用户集群。|
|[暂停集群升级](/intl.zh-CN/API参考/升级/暂停集群升级.md)

|调用PauseClusterUpgrade暂停用户集群升级。|
|[取消集群升级](/intl.zh-CN/API参考/升级/取消集群升级.md)

|调用CancelClusterUpgrade取消集群升级。|
|[恢复集群升级](/intl.zh-CN/API参考/升级/恢复集群升级.md)

|调用ResumeUpgradeCluster重新开始暂停集群的升级。|

## 应用

|API|描述|
|---|--|
|[创建编排模板](/intl.zh-CN/API参考/应用/创建编排模板.md)

|调用CreateTemplate创建一个模板。|
|[查询所有编排模板](/intl.zh-CN/API参考/应用/查询所有编排模板.md)

|调用DescribeTemplates查询编排模板。|
|[更新编排模板](/intl.zh-CN/API参考/应用/更新编排模板.md)

|调用UpdateTemplate更新一个编排模板。|
|[删除编排模板](/intl.zh-CN/API参考/应用/删除编排模板.md)

|调用DeleteTemplate删除指定模板。|
|[创建触发器](/intl.zh-CN/API参考/应用/创建触发器.md)|调用CreateKubernetesTrigger为应用创建触发器。|
|[查询触发器](/intl.zh-CN/API参考/应用/查询触发器.md)|调用GetKubernetesTrigger查询指定应用触发器。|
|[删除触发器](/intl.zh-CN/API参考/应用/删除触发器.md)|调用DeleteKubernetesTrigger删除应用触发器。|
|[查询编排模板详情](/intl.zh-CN/API参考/应用/查询编排模板详情.md)|调用DescribeTemplateAttribute查看指定模板的详细信息。|

## 组件

|API|描述|
|---|--|
|[安装集群组件](/intl.zh-CN/API参考/组件/安装集群组件.md)

|调用InstallClusterAddons为指定集群安装组件。|
|[查询集群组件版本](/intl.zh-CN/API参考/组件/查询集群组件版本.md)

|调用DescribeClusterAddonsVersion查询指定集群的组件版本。|
|[查询集群组件详情](/intl.zh-CN/API参考/组件/查询集群组件详情.md)

|调用DescribeAddons查询集群安装的组件详情。|
|[升级集群组件](/intl.zh-CN/API参考/组件/升级集群组件.md)|调用UpgradeClusterAddons将指定组件升级到指定版本。|
|[查询集群组件升级状态](/intl.zh-CN/API参考/组件/查询集群组件升级状态.md)|调用DescribeClusterAddonsUpgradeStatus同时查询多个组件的升级状态。|
|[修改集群托管侧组件参数](/intl.zh-CN/API参考/组件/修改集群托管侧组件参数.md)|调用ModifyClusterConfiguration修改托管侧组件的参数，下发ConfigMap到托管侧。|
|[卸载集群组件](/intl.zh-CN/API参考/组件/卸载集群组件.md)

|调用UnInstallClusterAddons卸载指定集群的组件。|

## 标签

|API|描述|
|---|--|
|[绑定资源标签](/intl.zh-CN/API参考/标签/绑定资源标签.md)

|调用TagResources为指定的集群绑定特定标签。|
|[修改集群标签](/intl.zh-CN/API参考/标签/修改集群标签.md)

|调用ModifyClusterTags修改当前Kubernetes集群的标签。|
|[查询资源标签列表](/intl.zh-CN/API参考/标签/查询资源标签列表.md)

|调用ListTagResources查询可见的资源标签关系。|
|[解绑资源标签](/intl.zh-CN/API参考/标签/解绑资源标签.md)

|调用UntagResources为指定的集群解绑标签。|

