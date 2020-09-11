---
keyword: [RAM角色授权, 权限回收, WorkerRolePolicy权限]
---

# 托管集群节点RAM角色收敛公告

当前托管集群节点默认的WorkerRolePolicy权限较大，为了进一步加强托管集群节点在多租户场景下的安全隔离性，容器服务Kubernetes版ACK（Container Service for Kubernetes）已收敛托管集群节点RAM角色绑定的权限。

## 角色授权

原有的RAM角色绑定权限被收敛后，原有节点角色中用于集群系统addon组件的权限策略将被删除，同时增加addon各组件对应的ACK系统角色。角色权限收敛后您在ACK控制台创建托管集群时，系统会提示进行以下系统角色授权，您可以使用主账号或具有[AliyunRAMFullAccess](https://ram.console.aliyun.com/policies/AliyunRAMFullAccess/System)或[AdministratorAccess](https://ram.console.aliyun.com/policies/AdministratorAccess/System)授权的子账号单击**前往RAM进行授权**，进入RAM的批量授权页面进行授权。

**说明：** 如果您使用OpenAPI创建集群，请使用[授权链接](https://ur.alipay.com/1paTcxSWdAEW70GVH5TZiO)进行授权。

![角色授权](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/3836359951/p101447.png)

在批量授权页面最下方，单击**同意授权**后，重新登录[容器服务管理控制台](https://cs.console.aliyun.com)即可创建集群。

![角色授权](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4836359951/p101452.png)

上述批量授权将增加以下系统角色的授权，用于集群addon组件的OpenAPI调用：

-   AliyunCSManagedLogRole
-   AliyunCSManagedCmsRole
-   AliyunCSManagedCsiRole
-   AliyunCSManagedVKRole
-   AliyunCSManagedNetworkRole
-   AliyunCSManagedArmsRole

收敛后的默认WorkerRole的RAM策略定义如下。

```
{
  "Version": "1",
  "Statement": [{
      "Action": [
        "ecs:DescribeInstanceAttribute",
        "ecs:DescribeInstanceTypesNew",
        "ecs:DescribeInstances"
      ],
      "Resource": [
        "*"
      ],
      "Effect": "Allow"
    },
    {
      "Action": [
        "log:GetProject",
        "log:GetLogStore",
        "log:GetConfig",
        "log:GetMachineGroup",
        "log:GetAppliedMachineGroups",
        "log:GetAppliedConfigs",
        "log:GetIndex",
        "log:GetSavedSearch",
        "log:GetDashboard",
        "log:GetJob"
      ],
      "Resource": [
        "*"
      ],
      "Effect": "Allow"
    },
    {
      "Action": [
        "cr:GetAuthorizationToken",
        "cr:ListInstanceEndpoint",
        "cr:PullRepository"
      ],  
      "Resource": [
        "*"
      ],
      "Effect": "Allow"
     }
  ]
}
              
```

