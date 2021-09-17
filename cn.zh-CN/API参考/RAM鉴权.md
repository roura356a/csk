# RAM鉴权

在使用RAM账号调用阿里云API前，需要主账号（即阿里云账号）通过创建授权策略对RAM账号进行授权。

## 资源授权

默认RAM账号没有权限通过调用阿里云API去创建、修改云资源。使用RAM账号调用API时，您需要先创建一个授权策略，然后将这个授权策略关联给对应的RAM账号完成资源授权。

在创建授权策略时，您可以通过ARN（Aliyun Resource Name）指定要授权的资源。ARN是阿里云为每个资源定义的一个全局的阿里云资源名称。

ARN格式如下。

```
acs:service-name:region:account-id:resource-relative-id
```

其中：

-   acs：Alibaba Cloud Service的首字母缩写，表示阿里云的公共云平台。
-   service-name：阿里云云服务的名称，如ECS、OSS、SLB等。
-   region：地域信息。如果不支持该项，可以使用通配符星号（\*）来代替。

-   account-id：账号ID，例如1234567890123456。

-   resource-relative-id：具体的资源描述，不同的云产品的资源描述也不同，详情参见各云产品的开发文档。

    例如`acs:oss::1234567890123456:sample_bucket/file1.txt`表示OSS服务中对象名称是sample\_bucket/file1.txt的资源，对象所有者的云账号ID为`1234567890123456`。


## 可授权的容器服务Kubernetes版类型

|资源类型|授权策略中的资源描述方法|
|----|------------|
|授予单集群权限|```
"Resource": [
     "acs:cs:*:*:cluster/集群ID"
 ]
``` |
|授予多个集群的权限|```
"Resource": [
     "acs:cs:*:*:cluster/集群ID",
     "acs:cs:*:*:cluster/集群ID"
 ]
``` |
|授予所有集群的权限|```
"Resource": [
     "*"
 ]
``` |

## API名称和RAM Action的对应关系

容器服务Kubernetes版中API名称和RAM Action及其描述的对应关系如下表所示：

|API名称|RAM Action|说明|是否支持限制集群|
|-----|----------|--|--------|
|DescribeEvents|`cs:DescribeEvents`|查询用户操作事件。|不支持|
|StartAlert|`cs:StartAlert`|启动报警规则。|不支持|
|StopAlert|`cs:StopAlert`|停止报警规则。|不支持|
|UpdateContactGroupForAlert|`cs:UpdateContactGroupForAlert`|更新告警联系人组。|不支持|
|DeleteAlertContact|`cs:DeleteAlertContact`|删除报警联系人。|不支持|
|DeleteAlertContactGroup|`cs:DeleteAlertContactGroup`|删除报警联系人分组。|不支持|
|DescribeUserPermission|`cs:DescribeUserPermission`|查询指定RAM用户的集群授权信息。|不支持|
|OpenAckService|`cs:OpenAckService`|开通容器服务ACK。|不支持|
|GrantPermissions|`cs:GrantPermissions`|全量更新RAM用户集群授权信息。|不支持|
|CreateCluster|`cs:CreateCluster`|创建ACK集群（包括ACK 专有版集群、ACK托管版集群、ASK集群，ACK边缘托管版集群以及注册集群）。|不支持|
|DescribeClusterResources|`cs:DescribeClusterResources`|根据集群ID查询该集群的所有资源。|支持|
|DescribeClusterDetail|`cs:DescribeClusterDetail`|根据集群ID查询该集群的详情。|支持|
|DescribeUserQuota|`cs:DescribeUserQuota`|查询集群及节点配额。|不支持|
|DescribeClustersV1|`cs:DescribeClustersV1`和`cs:GetClusters`|查询已创建的所有ACK集群的详情。|不支持|
|DescribeExternalAgent|`cs:DescribeExternalAgent`|根据集群ID查询集群中部署注册集群的代理配置。|支持|
|DescribeClusterLogs|`cs:DescribeClusterLogs`|根据集群ID查询集群日志。|支持|
|DescribeTaskInfo|`cs:DescribeTaskInfo`|根据任务ID查询该任务执行详情。|不支持|
|DescribeKubernetesVersionMetadata|`cs:DescribeKubernetesVersionMetadata`|查询ACK支持的Kubernetes版本详情。|不支持|
|DescribeClusterUserKubeconfig|`cs:DescribeClusterUserKubeconfig`|根据集群ID查询访问该集群的kubeconfig配置。|支持|
|DescribeClusterAddonUpgradeStatus|`cs:DescribeClusterAddonUpgradeStatus`|查询集群Addons升级状态。|支持|
|DescribeClusters|`cs:DescribeClusters`和`cs:GetClusters`|查看容器服务中创建的所有集群（包括Swarm和Kubernetes集群）。|不支持|
|DescribeClusterNamespaces|`cs:DescribeClusterNamespaces`|获取集群命名空间。|支持|
|ScaleOutCluster|`cs:ScaleOutCluster`|根据集群ID扩容集群。|支持|
|ModifyCluster|`cs:ModifyCluster`|根据集群ID修改该集群配置。|支持|
|MigrateCluster|`cs:MigrateCluster`|迁移集群。|支持|
|ScaleCluster|`cs:ScaleCluster`|扩容节点。|支持|
|UpdateK8sClusterUserConfigExpire|`cs:UpdateK8sClusterUserConfigExpire`|更新用户自定义配置过期时间。|支持|
|DeleteCluster|`cs:DeleteCluster`|根据集群ID删除集群实例，并释放集群所有节点资源。|支持|
|DescribeClusterNodes|`cs:DescribeClusterNodes`|根据集群ID查询该集群中所有节点的详情。|支持|
|AttachInstances|`cs:AttachInstances`|添加已有ECS实例到ACK集群。|支持|
|DescribeClusterAttachScripts|`cs:DescribeClusterAttachScripts`|查询手动添加实例到集群的脚本。|支持|
|DeleteClusterNodes|`cs:DeleteClusterNodes`|根据节点名称移除集群中指定节点。|支持|
|RemoveClusterNodes|`cs:RemoveClusterNodes`|移除指定集群额外节点。|支持|
|CreateClusterNodePool|`cs:CreateClusterNodePool`|为集群新建节点池。|支持|
|DescribeClusterNodePools|`cs:DescribeClusterNodePools`|根据集群ID查询该集群中所有节点池的详情。|支持|
|DescribeClusterNodePoolDetail|`cs:DescribeClusterNodePoolDetail`|根据节点池ID查询集群中该节点池的详情。|支持|
|ScaleClusterNodePool|`cs:ScaleClusterNodePool`|根据节点池ID扩容节点池节点。|支持|
|ModifyClusterNodePool|`cs:ModifyClusterNodePool`|根据节点池ID修改指定集群该节点池的配置。|支持|
|DeleteClusterNodepool|`cs:DeleteClusterNodepool`|根据节点池ID删除集群节点池。|支持|
|GetUpgradeStatus|`cs:GetUpgradeStatus`|根据集群ID查询该集群的升级状态。|支持|
|ResumeUpgradeCluster|`cs:ResumeUpgradeCluster`|根据集群ID恢复升级处于升级暂停状态的集群。|支持|
|UpgradeCluster|`cs:UpgradeCluster`|根据集群ID升级指定集群。|支持|
|PauseClusterUpgrade|`cs:PauseClusterUpgrade`|暂停集群升级。|支持|
|CancelClusterUpgrade|`cs:CancelClusterUpgrade`|取消处于升级状态的ACK集群。|支持|
|CreateTemplate|`cs:CreateTemplate`|创建一个编排模板。|不支持|
|DescribeTemplates|`cs:DescribeTemplates`|查询已创建的所有编排模板详情。|不支持|
|DescribeTemplateAttribute|`cs:DescribeTemplateAttribute`|根据编排模板ID查询该编排模板的详情。|不支持|
|UpdateTemplate|`cs:UpdateTemplate`|根据编排模板ID更新编排模板。|不支持|
|DeleteTemplate|`cs:DeleteTemplate`|根据编排模板ID删除指定编排模板。|不支持|
|InstallClusterAddons|`cs:InstallClusterAddons`|为集群安装组件。|支持|
|DescribeAddons|`cs:DescribeAddons`|查询平台支持的所有组件的详情。|支持|
|DescribeClusterAddonsUpgradeStatus|`cs:DescribeClusterAddonsUpgradeStatus`|根据组件名称查询该组件升级状态。|支持|
|DescribeClusterAddonsVersion|`cs:DescribeClusterAddonsVersion`|根据集群ID查询集群中已安装的所有组件的详情。|支持|
|ModifyClusterConfiguration|`cs:ModifyClusterConfiguration`|修改托管版集群配置。|支持|
|UpgradeClusterAddons|`cs:UpgradeClusterAddons`|根据组件名称将指定组件升级到指定版本。|支持|
|PauseComponentUpgrade|`cs:PauseComponentUpgrade`|暂停组件升级。|支持|
|ResumeComponentUpgrade|`cs:ResumeComponentUpgrade`|重新开始组件升级。|支持|
|CancelComponentUpgrade|`cs:CancelComponentUpgrade`|取消集群组件升级。|支持|
|UnInstallClusterAddons|`cs:UnInstallClusterAddons`|根据组件名称卸载指定集群的组件。|支持|
|ListTagResources|`cs:ListTagResources`|根据集群ID查询指定集群资源的标签。|不支持|
|TagResources|`cs:TagResources`|为资源绑定标签。|不支持|
|ModifyClusterTags|`cs:ModifyClusterTags`|根据集群ID修改该集群的集群标签。|支持|
|UntagResources|`cs:UntagResources`|删除资源标签。|不支持|
|CreateTrigger|`cs:CreateTrigger`|创建触发器。|支持|
|DescribeTrigger|`cs:DescribeTrigger`|查询触发器。|支持|
|DeleteTrigger|`cs:DeleteTrigger`|删除触发器|支持|

