---
keyword: [assign RAM roles, reduce permissions, WorkerRolePolicy]
---

# \[Product Changes\] ACK reduces the permissions of worker RAM roles in managed Kubernetes clusters

The default permission policy WorkerRolePolicy attached to worker roles in a managed Kubernetes cluster has excessive permissions. To improve data security and resource isolation in multi-tenancy scenarios, Container Service for Kubernetes \(ACK\) reduces the permissions of Resource Access Management \(RAM\) roles assigned to the worker nodes in managed Kubernetes clusters.

## Assign RAM roles

ACK removes the permissions required for add-on management from worker RAM roles, and introduces new system roles to manage permissions on different add-ons. After the permissions are reduced, the following message appears when you create new managed Kubernetes clusters in the ACK console. You can log on with your Alibaba Cloud account or as a RAM user that is attached with the [AliyunRAMFullAccess](https://ram.console.aliyun.com/policies/AliyunRAMFullAccess/System) or [AdministratorAccess](https://ram.console.aliyun.com/policies/AdministratorAccess/System) policy and click **Go to RAM console** to perform the authorization.

**Note:** If you create ACK clusters by calling the API, click [here](https://ur.alipay.com/1paTcxSWdAEW70GVH5TZiO) to assign the required RAM roles.

![Assign RAM roles](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2836359951/p101447.png)

At the bottom of the Cloud Resource Access Authorization page, click **Confirm Authorization Policy**. Then, log on to [the ACK console](https://cs.console.aliyun.com) again and create ACK clusters.

![Assign RAM roles](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2836359951/p101452.png)

The preceding operation assigns the following system roles to your account. These roles are required for add-on management by calling the API.

-   AliyunCSManagedLogRole
-   AliyunCSManagedCmsRole
-   AliyunCSManagedCsiRole
-   AliyunCSManagedVKRole
-   AliyunCSManagedNetworkRole
-   AliyunCSManagedArmsRole

The following code block shows the default RAM policy that is attached to the worker RAM roles of your ACK cluster after the permissions are reduced:

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

