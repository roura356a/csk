---
keyword: [Logtail-ds, 日志服务, 安装Logtail]
---

# 安装和使用Logtail

阿里云容器服务ACK集群集成了日志服务。您可在创建集群时启用日志服务，快速采集ACK集群的容器日志，包括容器的标准输出以及容器内的文本文件。本文介绍如何安装和使用Logtail组件。

## 步骤一：设置权限

在注册集群中安装组件前，您需要在接入集群中设置AK用来访问云服务的权限。设置AK前，您需要创建RAM用户并为其添加访问相关云资源的权限。

1.  创建RAM用户。

    有关如何创建RAM用户的具体步骤，请参见[创建RAM用户](/intl.zh-CN/用户管理/创建RAM用户.md)。

2.  创建权限策略。

    有关创建权限策略的具体操作步骤，请参见[创建自定义策略](/intl.zh-CN/权限策略管理/自定义策略/创建自定义策略.md)。

    Logtail组件需要的权限策略信息如下。

    ```
    {
        "Version": "1",
        "Statement": [
            {
                "Action": [
                    "log:CreateProject",
                    "log:GetProject",
                    "log:DeleteProject",
                    "log:CreateLogStore",
                    "log:GetLogStore",
                    "log:UpdateLogStore",
                    "log:DeleteLogStore",
                    "log:CreateConfig",
                    "log:UpdateConfig",
                    "log:GetConfig",
                    "log:DeleteConfig",
                    "log:CreateMachineGroup",
                    "log:UpdateMachineGroup",
                    "log:GetMachineGroup",
                    "log:DeleteMachineGroup",
                    "log:ApplyConfigToGroup",
                    "log:GetAppliedMachineGroups",
                    "log:GetAppliedConfigs",
                    "log:RemoveConfigFromMachineGroup",
                    "log:CreateIndex",
                    "log:GetIndex",
                    "log:UpdateIndex",
                    "log:DeleteIndex",
                    "log:CreateSavedSearch",
                    "log:GetSavedSearch",
                    "log:UpdateSavedSearch",
                    "log:DeleteSavedSearch",
                    "log:CreateDashboard",
                    "log:GetDashboard",
                    "log:UpdateDashboard",
                    "log:DeleteDashboard",
                    "log:CreateJob",
                    "log:GetJob",
                    "log:DeleteJob",
                    "log:UpdateJob",
                    "log:PostLogStoreLogs",
                    "log:CreateSortedSubStore",
                    "log:GetSortedSubStore",
                    "log:ListSortedSubStore",
                    "log:UpdateSortedSubStore",
                    "log:DeleteSortedSubStore",
                    "log:CreateApp",
                    "log:UpdateApp",
                    "log:GetApp",
                    "log:DeleteApp",
                    "cs:DescribeTemplates",
                    "cs:DescribeTemplateAttribute"
                ],
                "Resource": [
                    "*"
                ],
                "Effect": "Allow"
            }
        ]
    }
    ```

3.  为RAM用户添加权限。

    有关如何为RAM用户授权的具体步骤，请参见[为RAM用户授权](/intl.zh-CN/用户管理/为RAM用户授权.md)。

4.  为RAM用户创建AK。

    有关如何为RAM用户创建AK，请参见[获取AccessKey]()。


创建Logtail组件使用的Secret的代码如下。

```
kubectl -n kube-system create secret generic alibaba-addon-secret --from-literal='access-key-id=<your AccessKey ID>' --from-literal='access-key-secret=<your AccessKey Secret>'
```

**说明：**

您需要将上述代码中`<your AccessKey ID>`和`<your AccessKey Secret>`替换为您获取的AK信息。

## 步骤二：安装组件

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在集群列表页面，选择目标集群，并在目标集群右侧**操作**列下，选择**更多** \> **系统组件管理**。

3.  找到**logtail-ds**组件，然后单击右侧的**安装**。


## 步骤三：配置日志服务

有关如何在创建应用时配置日志服务的详情，请参见[创建应用时配置日志服务](/intl.zh-CN/Kubernetes集群用户指南/可观测性/日志管理/通过日志服务采集Kubernetes容器日志.md)。

**相关文档**  


[组件安装必读](/intl.zh-CN/Kubernetes集群用户指南/多云混合云管理/组件管理/组件安装必读.md)

