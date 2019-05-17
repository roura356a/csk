# 控制平面自定义RAM授权策略 {#concept_kry_ts2_xdb .concept}

本文档介绍控制平面如何创建自定义授权策略。下面以授予子账号查询、扩容和删除集群的权限为例进行说明。

## 背景信息 {#section_f6k_k07_6tr .section}

容器服务提供的系统授权策略的授权粒度比较粗，如果这种粗粒度授权策略不能满足您的需要，那么您可以创建自定义授权策略。例如，您想控制对某个具体的集群的操作权限，您必须使用自定义授权策略才能满足这种细粒度要求。

## 前提条件 {#section_azw_5s2_xdb .section}

在创建自定义授权策略时，您需要了解授权策略语言的基本结构和语法，相关内容的详细描述请参考[授权策略语言描述](https://www.alibabacloud.com/help/zh/doc-detail/28663.htm)。

## 操作步骤 {#section_s13_3jm_43b .section}

1.  使用具有RAM权限的账号登录[RAM管理控制台](https://ram.console.aliyun.com/)。
2.  单击左侧导航栏的**权限管理** \> **权限策略管理**，进入权限策略管理页面。
3.  单击**新建授权策略**，进入新建自定义权限策略页面。
4.  填写策略名称，配置模式选择脚本配置，并在策略内容中编写您的授权策略内容。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16637/155805767110480_zh-CN.png)

    ``` {#codeblock_i0a_ajh_b1u}
    {
     "Statement": [{
         "Action": [
             "cs:Get*",
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

    -   `Action` 处填写您所要授予的权限。

        **Note:** 所有的 Action 均支持通配符。

    -   `Resource` 有如下配置方式。
        -   授予单集群权限

            ``` {#codeblock_j26_0tz_86s}
            "Resource": [
                 "acs:cs:*:*:cluster/集群ID"
             ]
            ```

        -   授予多个集群权限

            ``` {#codeblock_1or_p1i_itt}
            "Resource": [
                 "acs:cs:*:*:cluster/集群ID",
                 "acs:cs:*:*:cluster/集群ID"
             ]
            ```

        -   授予您所有集群的权限

            ``` {#codeblock_xuz_nle_xbh}
            "Resource": [
                 "*"
             ]
            ```

            其中，`集群ID` 需要替换为您要授权的真实的集群 ID。

5.  编写完毕后，单击**确定**。

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

**预期结果**

返回权限策略管理页面，在搜索框中搜索策略名或备注，可以看到您授权的自定义的策略。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16637/155805767147367_zh-CN.png)

