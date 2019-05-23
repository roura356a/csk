# Kubernetes 集群访问控制授权概述 {#concept_268689 .concept}

本文总体介绍容器服务 Kubernetes 集群访问控制授权的组成及如何进行授权。

容器 Kubernetes集群授权分为 RAM 授权和 RBAC 授权两部分。

## RAM授权 {#section_xgk_3k1_gz9 .section}

RAM 授权作用于集群管理接口的访问控制，当您需要对集群进行可见性、扩缩容、添加节点等操作时，需要进行[RAM授权](intl.zh-CN/用户指南/Kubernetes集群/授权管理/自定义RAM授权策略.md#)。详细权限见下表：

|Action|说明|
|------|--|
|CreateCluster|创建集群|
|AttachInstances|向集群中添加已有ECS实例|
|ScaleCluster|扩容集群|
|GetClusters|查看集群列表|
|GetClusterById|查看集群详情|
|ModifyClusterName|修改集群名称|
|DeleteCluster|删除集群|
|UpgradeClusterAgent|升级集群Agent|
|GetClusterLogs|查看集群的操作日志|
|GetClusterEndpoint|查看集群接入点地址|
|GetClusterCerts|下载集群证书|
|RevokeClusterCerts|吊销集群证书|
|BindSLB|为集群绑定负载均衡实例|
|UnBindSLB|为集群解绑负载均衡实例|
|ReBindSecurityGroup|为集群重新绑定安全组|
|CheckSecurityGroup|检测集群现有的安全组规则|
|FixSecurityGroup|修复集群的安全组规则|
|ResetClusterNode|重置集群中的节点|
|DeleteClusterNode|移除集群中的节点|
|CreateAutoScale|创建节点弹性伸缩规则|
|UpdateAutoScale|更新节点弹性伸缩规则|
|DeleteAutoScale|删除节点弹性伸缩规则|
|GetClusterProjects|查看集群下的应用|
|CreateTriggerHook|为应用创建触发器|
|GetTriggerHook|查看应用的触发器列表|
|RevokeTriggerHook|删除应用的触发器|
|CreateClusterToken|创建 Token|

## RBAC 授权 {#section_j5k_rqk_6va .section}

容器服务 Kubernetes 的 RBAC 授权是指用户所有调用 apiserver 访问 Kubernetes 集群内资源模型的访问控制，请参考[授权概述](https://kubernetes.io/zh/docs/reference/access-authn-authz/authorization/)，你可以通过容器服务给子账号授予如下预置角色：

|角色|集群内RBAC权限|
|--|---------|
|管理员|对所有命名空间下所有资源的读写权限|
|运维人员|对所有命名空间下控制台可见资源的读写权限，对集群节点，存储卷，命名空间，配额的只读权限|
|开发人员|对所有命名空间或所选命名空间下控制台可见资源的读写权限|
|受限用户|对所有命名空间或所选命名空间下控制台可见资源的只读权限|
|自定义|权限由您所选择的 ClusterRole 决定，请在确定所选 ClusterRole 对各类资源的操作权限后再进行授权，以免子账号获得不符合预期的权限|

## 如何授权 {#section_bmd_wzi_cc4 .section}

-   在进行子账号集群RBAC授权前请首先完成对集群管控能力的RAM授权，您可以根据需要授予目标集群的读写策略：

    -   读策略：用于查看集群配置，kubeconfig 等基本信息
    -   写策略：包含集群伸缩、升级、删除，添加节点等集群管控能力
    在提交RBAC授权前，您需要确保目标集群已经被授予RAM只读权限，策略参考如下：

    ``` {#codeblock_tjz_rxw_kqm}
    {
      "Statement": [
        {
          "Action": "cs:Get*",
          "Effect": "Allow",
          "Resource": [
            "acs:cs:*:*:cluster/<yourclusterID>"
          ]
        }
      ],
      "Version": "1"
    }
    ```

    具体的RAM授权步骤请参见[控制平面自定义RAM授权策略](intl.zh-CN/用户指南/Kubernetes集群/授权管理/自定义RAM授权策略.md#)。

-   当您完成RAM授权后，可参见[子账号RBAC权限配置指导](intl.zh-CN/用户指南/Kubernetes集群/授权管理/子账号RBAC权限配置指导.md#)完成集群内 Kubernetes 资源模型访问的 RBAC 授权。

