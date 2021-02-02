# RAM鉴权

在使用RAM账号调用阿里云API前，需要主账号通过创建授权策略对RAM账号进行授权。

## 资源授权

默认RAM账号没有权限通过调用阿里云API去创建、修改云资源。使用RAM账号调用API时，您需要先创建一个授权策略，然后将这个授权策略关联给对应的RAM账号完成资源授权。

在创建授权策略时，您可以通过ARN \(Aliyun Resource Name\) 指定要授权的资源。ARN是阿里云为每个资源定义的一个全局的阿里云资源名称。

ARN格式如下。

```
acs:service-name:region:account-id:resource-relative-id
```

其中：

-   acs：Alibaba Cloud Service的首字母缩写，表示阿里云的公共云平台。
-   service-name：阿里云云服务的名称，如ecs, oss, slb等。
-   region：地域信息。如果不支持该项，可以使用通配符星号（\*）来代替。

-   account-id：账号ID，例如1234567890123456。

-   resource-relative-id：具体的资源描述，不同的云产品的资源描述也不同，详情参见各云产品的开发文档。

    例如`acs:oss::1234567890123456:sample_bucket/file1.txt`表示OSS服务中对象名称是sample\_bucket/file1.txt的资源，对象的所有者是云账号ID为`1234567890123456`。


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

容器服务Kubernetes版中API名称和RAM Action及其描述的对应关系如下表。

|API 名称|RAM Action|说明|
|------|----------|--|
|AttachInstances|AttachInstances|添加已有ECS实例到Kubernetes集群。|
|CancelClusterUpgrade|CancelK8sCluster|取消升级集群。|
|CancelComponentUpgrade|CancelComponentUpgrade|取消升级组件。|
|CreateCluster|CreateCluster|创建Kubernetes集群。|
|CreateKubernetesTrigger|GenerateTriggerHook|创建应用触发器。|
|CreateTemplate|CreateTemplate|创建应用部署模板。|
|DeleteCluster|DeleteCluster|删除集群。|
|DeleteClusterNodes|DeleteClusterNodes|移除节点并释放ECS。|
|DeleteKubernetesTrigger|RevokeTriggers|删除应用触发器。|
|DeleteTemplate|V2DeleteTemplateInfo|删除应用部署模板。|
|DescribeAddons|Queryk8sComponentsVersion|查询集群组件信息。|
|DescribeClusterAddonsVersion|Queryk8sComponentsUpdateVersion|查询集群组件版本信息。|
|DescribeClusterAddonUpgradeStatus|QueryK8sComponentUpgradeStatus|查询集群组件升级状态。|
|DescribeClusterAttachScripts|GetClusterJoinScript|获取手动添加节点到Kubernetes集群的脚本。|
|DescribeClusterDetail|GetClusterById|查询集群实例。|
|DescribeClusterLogs|GetClusterLogs|查看集群日志。|
|DescribeClusterNodes|DescribeClusterNodes|查看集群节点。|
|DescribeClusterResources|DescribeClusterResources|查看集群资源。|
|DescribeClusters|GetClustersByUid和GetClusters|查询所有集群实例。|
|DescribeClustersV1|ListClusters|查询所有集群实例。|
|DescribeClusterUserKubeconfig|GetUserConfig|获取集群kubeconfig。|
|DescribeClusterV2UserKubeconfig|GetUserConfig|获取集群kubeconfig。|
|DescribeExternalAgent|DescribeExternalClusterAgentDeployment|查看注册集群的集群导入代理配置。|
|DescribeTemplates|V2ListTemplates|查看应用部署模板。|
|DescribeUserQuota|GetUserQuota|查看用户配额。|
|GetKubernetesTrigger|GetK8sTrigger|获取应用触发器详情|
|GetUpgradeStatus|GetK8sClusterState|查看集群升级状态。|
|InstallClusterAddons|InstallK8sComponents|安装集群插件。|
|ModifyCluster|ModifyCluster|修改集群信息。|
|ModifyClusterTags|UpdateClusterTags|修改集群标签。|
|PauseClusterUpgrade|UpgradeCluster|暂停集群升级。|
|PauseComponentUpgrade|PauseComponentUpgrade|暂停组件升级。|
|ReBindSecurityGroup|ReBindSecurityGroup|重新绑定安全组。|
|RemoveClusterNodes|DeleteClusterNode|移除集群节点。|
|ResumeComponentUpgrade|ResumeComponentUpgrade|重新开始组件升级。|
|ResumeUpgradeCluster|UpgradeCluster|重新开始集群升级。|
|ScaleCluster|ScaleCluster|集群扩容。|
|ScaleOutCluster|ScaleCluster|添加已有节点。|
|UnInstallClusterAddons|UnInstallK8sComponents|卸载集群组件。|
|UpdateK8sClusterUserConfigExpire|UpdateK8sClusterUserConfigExpire|更新用户自定义配置过期时间。|
|UpgradeCluster|UpgradeCluster|升级集群。|
|UpgradeClusterAddons|UpgradeK8sComponents|升级集群插件。|
|DescribeUserPermission|GetUserPermissions|获取RAM用户集群授权信息。|
|GrantPermissions|GrantPermission|全量更新RAM用户集群授权信息。|

