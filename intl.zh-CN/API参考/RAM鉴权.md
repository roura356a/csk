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

|API名称|RAM Action|说明|
|-----|----------|--|
|AttachInstances|`cs:AttachInstances`|添加已有ECS实例到Kubernetes集群。|
|CancelClusterUpgrade|`cs:CancelK8sCluster`|取消升级集群。|
|CancelComponentUpgrade|`cs:CancelComponentUpgrade`|取消升级组件。|
|CreateCluster|`cs:CreateCluster`|创建Kubernetes集群。|
|CreateTriggerHook|`cs:CreateKubernetesTrigger`|创建和修改应用触发器。|
|CreateTemplate|`cs:CreateTemplate`|创建应用部署模板。|
|DeleteCluster|`cs:DeleteCluster`|删除集群。|
|DeleteClusterNodes|`cs:DeleteClusterNodes`|移除节点并释放ECS。|
|DeleteTrigger|`cs:RevokeTriggers`|删除应用触发器。|
|DeleteTemplate|`cs:V2DeleteTemplateInfo`|删除应用部署模板。|
|DescribeAddons|`cs:Queryk8sComponentsVersion`|查询集群组件信息。|
|DescribeClusterAddonsVersion|`cs:Queryk8sComponentsUpdateVersion`|查询集群组件版本信息。|
|DescribeClusterAddonUpgradeStatus|`cs:QueryK8sComponentUpgradeStatus`|查询集群组件升级状态。|
|DescribeClusterAttachScripts|`cs:GetClusterJoinScript`|获取手动添加节点到Kubernetes集群的脚本。|
|DescribeClusterDetail|`cs:GetClusterById`|查询集群实例。|
|DescribeClusterLogs|`cs:GetClusterLogs`|查看集群日志。|
|DescribeClusterNodes|`cs:DescribeClusterNodes`|查看集群节点。|
|DescribeClusterResources|`cs:DescribeClusterResources`|查看集群资源。|
|DescribeClusters|`cs:GetClustersByUid`和`cs:GetClusters`|查询所有集群实例。|
|DescribeClustersV1|`cs:ListClusters`|查询所有集群实例。|
|DescribeClusterUserKubeconfig|`cs:GetUserConfig`|获取集群kubeconfig。|
|DescribeClusterV2UserKubeconfig|`cs:GetUserConfig`|获取集群kubeconfig。|
|DescribeExternalAgent|`cs:DescribeExternalClusterAgentDeployment`|查看注册集群的集群导入代理配置。|
|DescribeTemplates|`cs:V2ListTemplates`|查看应用部署模板。|
|DescribeUserQuota|`cs:GetUserQuota`|查看用户配额。|
|GetTrigger|`cs:GetK8sTrigger`|获取应用触发器详情。|
|GetUpgradeStatus|`cs:GetK8sClusterState`|查看集群升级状态。|
|InstallClusterAddons|`cs:InstallK8sComponents`|安装集群插件。|
|ModifyCluster|`cs:ModifyCluster`|修改集群信息。|
|ModifyClusterTags|`cs:UpdateClusterTags`|修改集群标签。|
|PauseClusterUpgrade|`cs:UpgradeCluster`|暂停集群升级。|
|PauseComponentUpgrade|`cs:PauseComponentUpgrade`|暂停组件升级。|
|ReBindSecurityGroup|`cs:ReBindSecurityGroup`|重新绑定安全组。|
|RemoveClusterNodes|`cs:DeleteClusterNode`|移除集群节点。|
|ResumeComponentUpgrade|`cs:ResumeComponentUpgrade`|重新开始组件升级。|
|ResumeUpgradeCluster|`cs:UpgradeCluster`|重新开始集群升级。|
|ScaleOutCluster|`cs:ScaleCluster`|扩容集群。|
|UnInstallClusterAddons|`cs:UnInstallK8sComponents`|卸载集群组件。|
|UpdateK8sClusterUserConfigExpire|`cs:UpdateK8sClusterUserConfigExpire`|更新用户自定义配置过期时间。|
|UpgradeCluster|`cs:UpgradeCluster`|升级集群。|
|UpgradeClusterAddons|`cs:UpgradeK8sComponents`|升级集群插件。|
|DescribeUserPermission|`cs:GetUserPermissions`|获取RAM用户集群授权信息。|
|GrantPermissions|`cs:GrantPermission`|全量更新RAM用户集群授权信息。|
|CreateClusterNodePool|`cs:CreateNodepool`|创建节点池。|
|DeleteClusterNodepool|`cs:DeleteNodepool`|删除节点池。|
|DescribeClusterNodePoolDetail|`cs:GetNodepoolDetail`|获取节点池详细信息。|
|DescribeClusterNodePools|`cs:GetNodepools`|获取节点池信息。|
|ModifyClusterNodePool|`cs:UpdateNodepool`|修改节点池信息。|
|ScaleClusterNodePool|`cs:ScaleNodepool`|扩缩容节点池。|
|MigrateCluster|`cs:MigrateCluster`|迁移集群。|

