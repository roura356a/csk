---
keyword: [RAM, 自定义授权策略]
---

# 自定义RAM授权策略

本文档介绍如何创建自定义授权策略。下面以授予RAM用户（即子账号）查询、扩容和删除集群的权限为例进行说明。

在创建自定义授权策略时，您需要了解授权策略语言的基本结构和语法。更多信息，请参见[授权策略语言描述](https://www.alibabacloud.com/help/zh/doc-detail/28663.htm)。

容器服务ACK提供的系统授权策略的授权粒度比较粗，如果这种粗粒度授权策略不能满足您的需要，那么您可以创建自定义授权策略。例如，您想控制对某个具体的集群的操作权限，您必须使用自定义授权策略才能满足这种细粒度要求。

在进行RAM用户的集群RBAC授权前，您需要完成对集群管控能力的RAM授权。您可以根据需要授予RAM用户对于目标集群的读写策略：

-   读策略：用于查看集群配置、kubeconfig等基本信息。
-   写策略：包含集群伸缩、升级、删除、添加节点等集群管控能力。

在提交RBAC授权前，您需要确保目标集群已经被授予RAM只读权限，策略参考如下。

```
{
  "Statement": [
    {
      "Action": [
        "cs:Get*",
        "cs:List*",
        "cs:Describe*"
      ],
      "Effect": "Allow",
      "Resource": [
        "acs:cs:*:*:cluster/<yourclusterID>"
      ]
    }
  ],
  "Version": "1"
}
```

当您完成RAM授权后，需要完成集群内Kubernetes资源模型访问的RBAC授权。具体操作，请参见[配置RAM用户RBAC权限](/intl.zh-CN/Kubernetes集群用户指南/授权/配置RAM用户RBAC权限.md)。

## 创建自定义授权策略

1.  使用具有RAM权限的账号登录[RAM管理控制台](https://ram.console.aliyun.com/)。

2.  单击左侧导航栏的**权限管理** \> **权限策略管理**。

3.  在权限策略管理页面，单击**创建权限策略**。

4.  在新建自定义权限策略页面，填写**策略名称**，**配置模式**选择脚本配置，并在**策略内容**中编写您的授权策略内容。

    ![自定义权限策略](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/9075659951/p10480.png)

    ```
    {
     "Statement": [{
         "Action": [
             "cs:Get*",
             "cs:List*",
             "cs:Describe*",
             "cs:ScaleCluster",
             "cs:DeleteCluster"
         ],
         "Effect": "Allow",
         "Resource": [
             "acs:cs:*:*:cluster/集群ID"
         ]
     }],
     "Version": "1"
    }
    ```

    其中：

    -   `Action`处填写您所要授予的权限。

        **说明：** 所有的Action均支持通配符。

    -   `Resource`有以下配置方式。
        -   授予单集群权限

            ```
            "Resource": [
                 "acs:cs:*:*:cluster/集群ID"
             ]
            ```

        -   授予多个集群权限

            ```
            "Resource": [
                 "acs:cs:*:*:cluster/集群ID",
                 "acs:cs:*:*:cluster/集群ID"
             ]
            ```

        -   授予所有集群的权限

            ```
            "Resource": [
                 "*"
             ]
            ```

            其中，`集群ID`需要替换为您要授权的真实的集群ID。

5.  编写完毕后，单击**确定**。

    返回权限策略管理页面，在搜索框中搜索策略名或备注，可以看到您授权的自定义的策略。

    ![权限策略管理](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/0175659951/p47367.png)


## 相关参考

**RAM Action和API名称的对应关系**

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

**为RAM用户授权不支持限制集群的OpenAPI的操作权限**

部分OpenAPI不支持限制集群的授权（例如，DescribeEvents，详见上方的表格。），若您需要给RAM用户授权这些OpenAPI不支持限制集群的OpenAPI的操作权限，请不要在Resource中限定集群ID。

例如，当前RAM权限策略如下：

```
{
    "Statement": [
        {
            "Action": [
                "cs:Get*",
                "cs:List*",
                "cs:Describe*"
            ],
            "Effect": "Allow",
            "Resource": [
                "acs:cs:*:*:cluster/c2e63856bcd714197****"
            ]
        }
    ],
    "Version": "1"
}
```

若您需要授权不支持限制集群的OpenAPI DescribeEvents ，其对应的RAM Action为`cs:DescribeEvents`，修改后的RAM权限策略如下所示：

```
{
    "Statement": [
        {
            "Action": [
                "cs:DescribeEvents"
            ],
            "Effect": "Allow",
            "Resource": [
              "*"
            ]
        },
        {
            "Action": [
                "cs:Get*",
                "cs:List*",
                "cs:Describe*"
            ],
            "Effect": "Allow",
            "Resource": [
                "acs:cs:*:*:cluster/c2e63856bcd714197****"
            ]
        }
    ],
    "Version": "1"
}
```

