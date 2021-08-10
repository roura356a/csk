---
keyword: [OpenAPI鉴权, RAM Action]
---

# 【产品变更】容器服务OpenAPI鉴权收敛公告

阿里云容器服务Kubernetes版（ACK）将于8月16日开始陆续加强部分OpenAPI对RAM用户（即子账号）和角色的权限控制，请您及时检查阿里云账号（即主账号）下RAM用户或角色的绑定权限，添加可能遗漏的权限策略，避免未授权的RAM用户或角色访问OpenAPI发生鉴权错误。

## 影响范围

本次鉴权收敛完成后，若RAM用户或角色执行的操作没有对应权限，控制台或OpenAPI会提示包含RAM policy Forbidden或STSToken policy Forbidden的错误信息，同时错误信息中会包含所需权限的RAM Action名称。

错误信息示例（下面错误信息中包含的RAM Action名称为cs:DescribeClusterNamespaces）：

```
RAM policy Forbidden for action cs:DescribeClusterNamespaces
```

若您的RAM用户或角色使用了下表中的OpenAPI接口并且未授权，请您参考接口对应的RAM Action，及时前往RAM控制台添加指定RAM用户或角色的授权：

|API名称|RAM Action|说明|
|-----|----------|--|
|DescribeEvents|cs:DescribeEvents|[查询用户操作事件](https://next.api.alibabacloud.com/document/CS/2015-12-15/DescribeEvents?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fB1NhBR)|
|StartAlert|cs:StartAlert|[启动报警规则](https://next.api.alibabacloud.com/document/CS/2015-12-15/StartAlert?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fB1NhBR)|
|StopAlert|cs:StopAlert|[停止报警规则](https://next.api.alibabacloud.com/document/CS/2015-12-15/StopAlert?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fB1NhBR)|
|DeleteAlertContact|cs:DeleteAlertContact|[删除报警联系人](https://next.api.alibabacloud.com/document/CS/2015-12-15/DeleteAlertContact?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fB1NhBR)|
|DeleteAlertContactGroup|cs:DeleteAlertContactGroup|[删除报警联系人分组](https://next.api.alibabacloud.com/document/CS/2015-12-15/DeleteAlertContactGroup?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fB1NhBR)|
|OpenAckService|cs:OpenAckService|[开通容器服务ACK](https://next.api.alibabacloud.com/document/CS/2015-12-15/OpenAckService?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fB1NhBR)|
|DescribeClusterResources|cs:DescribeClusterResources|[根据集群ID查询该集群的所有资源](https://next.api.alibabacloud.com/document/CS/2015-12-15/DescribeClusterResources?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|DescribeUserQuota|cs:DescribeUserQuota|[查询集群及节点配额](https://next.api.alibabacloud.com/document/CS/2015-12-15/DescribeUserQuota?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|DescribeClustersV1|cs:DescribeClustersV1|[查询已创建的所有ACK集群的详情](https://next.api.alibabacloud.com/document/CS/2015-12-15/DescribeClustersV1?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|DescribeExternalAgent|cs:DescribeExternalAgent|[根据集群ID查询集群中部署注册集群的代理配置](https://next.api.alibabacloud.com/document/CS/2015-12-15/DescribeExternalAgent?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|DescribeKubernetesVersionMetadata|cs:DescribeKubernetesVersionMetadata|[查询ACK支持的Kubernetes版本详情](https://next.api.alibabacloud.com/document/CS/2015-12-15/DescribeKubernetesVersionMetadata?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|DescribeClusterAddonUpgradeStatus|cs:DescribeClusterAddonUpgradeStatus|[查询集群Addons升级状态](https://next.api.alibabacloud.com/document/CS/2015-12-15/DescribeClusterAddonUpgradeStatus?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|DescribeClusters|cs:DescribeClusters|[查看容器服务中创建的所有集群（包括Swarm和Kubernetes集群）](https://next.api.alibabacloud.com/document/CS/2015-12-15/DescribeClusters?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|DescribeClusterNamespaces|cs:DescribeClusterNamespaces|[获取群集命名空间](https://next.api.alibabacloud.com/document/CS/2015-12-15/DescribeClusterNamespaces?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|ModifyCluster|cs:ModifyCluster|[根据集群ID修改该集群配置](https://next.api.alibabacloud.com/document/CS/2015-12-15/ModifyCluster?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|MigrateCluster|cs:MigrateCluster|[迁移群集](https://next.api.alibabacloud.com/document/CS/2015-12-15/MigrateCluster?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|UpdateK8sClusterUserConfigExpire|cs:UpdateK8sClusterUserConfigExpire|[更新用户自定义配置过期时间](https://next.api.alibabacloud.com/document/CS/2015-12-15/UpdateK8sClusterUserConfigExpire?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|DescribeClusterNodes|cs:DescribeClusterNodes|[根据集群ID，查询该集群中的所有节点的详情](https://next.api.alibabacloud.com/document/CS/2015-12-15/DescribeClusterNodes?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|DescribeClusterAttachScripts|cs:DescribeClusterAttachScripts|[查询手动添加实例到集群的脚本](https://next.api.alibabacloud.com/document/CS/2015-12-15/DescribeClusterAttachScripts?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|GetUpgradeStatus|cs:GetUpgradeStatus|[根据集群ID，查询该集群的升级状态](https://next.api.alibabacloud.com/document/CS/2015-12-15/GetUpgradeStatus?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|UpgradeCluster|cs:UpgradeCluster|[根据集群ID，升级指定集群](https://next.api.alibabacloud.com/document/CS/2015-12-15/UpgradeCluster?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|PauseClusterUpgrade|cs:PauseClusterUpgrade|[暂停用户集群升级](https://next.api.alibabacloud.com/document/CS/2015-12-15/PauseClusterUpgrade?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|CancelClusterUpgrade|cs:CancelClusterUpgrade|[取消处于升级状态的ACK集群](https://next.api.alibabacloud.com/document/CS/2015-12-15/CancelClusterUpgrade?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|CreateTemplate|cs:CreateTemplate|[创建一个编排模板](https://next.api.alibabacloud.com/document/CS/2015-12-15/CreateTemplate?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|DescribeTemplates|cs:DescribeTemplates|[查询已创建的所有编排模板详情](https://next.api.alibabacloud.com/document/CS/2015-12-15/DescribeTemplates?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|DescribeTemplateAttribute|cs:DescribeTemplateAttribute|[根据编排模板ID，查询该编排模板的详情](https://next.api.alibabacloud.com/document/CS/2015-12-15/DescribeTemplateAttribute?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|UpdateTemplate|cs:UpdateTemplate|[根据编排模板ID，更新编排模板](https://next.api.alibabacloud.com/document/CS/2015-12-15/UpdateTemplate?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|DeleteTemplate|cs:DeleteTemplate|[根据编排模板ID，删除指定编排模板](https://next.api.alibabacloud.com/document/CS/2015-12-15/DeleteTemplate?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|CreateKubernetesTrigger|cs:CreateKubernetesTrigger|[为应用创建触发器](https://next.api.alibabacloud.com/document/CS/2015-12-15/CreateKubernetesTrigger?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|GetKubernetesTrigger|cs:GetKubernetesTrigger|[根据应用名称查询该应用的触发器](https://next.api.alibabacloud.com/document/CS/2015-12-15/GetKubernetesTrigger?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|DeleteKubernetesTrigger|cs:DeleteKubernetesTrigger|[根据应用触发器ID，删除应用触发器](https://next.api.alibabacloud.com/document/CS/2015-12-15/DeleteKubernetesTrigger?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|InstallClusterAddons|cs:InstallClusterAddons|[为集群安装组件](https://next.api.alibabacloud.com/document/CS/2015-12-15/InstallClusterAddons?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|DescribeAddons|cs:DescribeAddons|[查询平台支持的所有组件的详情](https://next.api.alibabacloud.com/document/CS/2015-12-15/DescribeAddons?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|DescribeClusterAddonsUpgradeStatus|cs:DescribeClusterAddonsUpgradeStatus|[根据组件名称查询该组件升级状态](https://next.api.alibabacloud.com/document/CS/2015-12-15/DescribeClusterAddonsUpgradeStatus?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|DescribeClusterAddonsVersion|cs:DescribeClusterAddonsVersion|[根据集群ID，查询集群中已安装的所有组件的详情](https://next.api.alibabacloud.com/document/CS/2015-12-15/DescribeClusterAddonsVersion?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|ModifyClusterConfiguration|cs:ModifyClusterConfiguration|[只针对托管版集群](https://next.api.alibabacloud.com/document/CS/2015-12-15/ModifyClusterConfiguration?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|UpgradeClusterAddons|cs:UpgradeClusterAddons|[根据组件名称，将指定组件升级到指定版本](https://next.api.alibabacloud.com/document/CS/2015-12-15/UpgradeClusterAddons?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|PauseComponentUpgrade|cs:PauseComponentUpgrade|[暂停组件升级](https://next.api.alibabacloud.com/document/CS/2015-12-15/PauseComponentUpgrade?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|ResumeComponentUpgrade|cs:ResumeComponentUpgrade|[重新开始组件升级](https://next.api.alibabacloud.com/document/CS/2015-12-15/ResumeComponentUpgrade?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|CancelComponentUpgrade|cs:CancelComponentUpgrade|[取消集群组件升级](https://next.api.alibabacloud.com/document/CS/2015-12-15/CancelComponentUpgrade?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|UnInstallClusterAddons|cs:UnInstallClusterAddons|[根据组件名称，卸载指定集群的组件](https://next.api.alibabacloud.com/document/CS/2015-12-15/UnInstallClusterAddons?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|
|CreateAutoscalingConfig|cs:CreateAutoscalingConfig|[创建自动伸缩配置](https://next.api.alibabacloud.com/document/CS/2015-12-15/CreateAutoscalingConfig?spm=api-workbench.Product%20Home%20Page.0.0.32e01e0fWDleYg)|

## RAM权限策略修改示例

本示例介绍如何修改RAM用户或角色的RAM权限策略。

例如，当前RAM用户或角色只拥有以下RAM权限策略：

```
{
 "Statement": [{
     "Action": [
         "cs:Get*"
     ],
     "Effect": "Allow",
     "Resource": [
         "acs:cs:*:*:*"
     ]
 }],
 "Version": "1"
}
```

可通过修改RAM权限策略增加集群列表的Open API（DescribeClustersV1）对应的RAM Action（cs:DescribeClustersV1），授权该RAM用户或角色查看集群列表的权限：

```
{
 "Statement": [{
     "Action": [
         "cs:Get*",
         "cs:DescribeClustersV1"
     ],
     "Effect": "Allow",
     "Resource": [
         "acs:cs:*:*:*"
     ]
 }],
 "Version": "1"
}
```

关于RAM授权的相关操作，请参见[自定义RAM授权策略](/cn.zh-CN/Kubernetes集群用户指南/授权/自定义RAM授权策略.md)。

