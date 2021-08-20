---
keyword: [RAM, 自定义授权策略]
---

# 自定义RAM授权策略

本文档介绍如何创建自定义授权策略。下面以授予RAM用户（即子账号）查询、扩容和删除集群的权限为例进行说明。

在创建自定义授权策略时，您需要了解授权策略语言的基本结构和语法。更多信息，请参见[授权策略语言描述](https://help.aliyun.com/document_detail/28663.html)。

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

当您完成RAM授权后，需要完成集群内Kubernetes资源模型访问的RBAC授权。具体操作，请参见[配置RAM用户RBAC权限](/cn.zh-CN/Kubernetes集群用户指南/授权/配置RAM用户RBAC权限.md)。

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

|API 名称|RAM Action|说明|
|------|----------|--|
|AttachInstances|AttachInstances|添加已有ECS实例到Kubernetes集群。|
|CancelClusterUpgrade|CancelK8sCluster|取消升级集群。|
|CancelComponentUpgrade|CancelComponentUpgrade|取消升级组件。|
|CreateCluster|CreateCluster|创建Kubernetes集群。|
|CreateTriggerHook|CreateKubernetesTrigger|创建和修改应用触发器。|
|CreateTemplate|CreateTemplate|创建应用部署模板。|
|DeleteCluster|DeleteCluster|删除集群。|
|DeleteClusterNodes|DeleteClusterNodes|移除节点并释放ECS。|
|DeleteTrigger|RevokeTriggers|删除应用触发器。|
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
|GetTrigger|GetK8sTrigger|获取应用触发器详情。|
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
|ScaleOutCluster|ScaleCluster|扩容集群。|
|UnInstallClusterAddons|UnInstallK8sComponents|卸载集群组件。|
|UpdateK8sClusterUserConfigExpire|UpdateK8sClusterUserConfigExpire|更新用户自定义配置过期时间。|
|UpgradeCluster|UpgradeCluster|升级集群。|
|UpgradeClusterAddons|UpgradeK8sComponents|升级集群插件。|
|DescribeUserPermission|GetUserPermissions|获取RAM用户集群授权信息。|
|GrantPermissions|GrantPermission|全量更新RAM用户集群授权信息。|
|CreateClusterNodePool|CreateNodepool|创建节点池。|
|DeleteClusterNodepool|DeleteNodepool|删除节点池。|
|DescribeClusterNodePoolDetail|GetNodepoolDetail|获取节点池详细信息。|
|DescribeClusterNodePools|GetNodepools|获取节点池信息。|
|ModifyClusterNodePool|UpdateNodepool|修改节点池信息。|
|ScaleClusterNodePool|ScaleNodepool|扩缩容节点池。|
|MigrateCluster|MigrateCluster|迁移集群。|

