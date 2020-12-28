# 授权管理FAQ

本文主要为您介绍授权管理过程的一些常见问题。

-   [如何在Kubernetes集群指定RAM中的自定义角色？](#section_0sm_z8q_eyo)
-   [子账号如何给其他子账号进行RBAC授权？](#section_wb5_8oc_wnd)

## 如何在Kubernetes集群指定RAM中的自定义角色？

Kubernetes集群暂不支持指定RAM中的自定义角色，但是在Kubernetes集群中创建worker时会自动生成一个角色，您可以通过给该角色添加policy的方式，来实现角色授权。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在**详情**页单击**基本信息**页签。

5.  在基本信息页面的**集群资源**区域，单击**Worker RAM角色**。

6.  跳转到RAM控制台页面，在RAM 角色管理页面，单击权限策略名称。

7.  进入策略权限管理页面，单击**修改策略内容**，将以下内容填入后，单击**确定**。本示例中授予该角色伸缩和删除集群的权限。如果您想了解更多的权限，请参见[自定义RAM授权策略](/cn.zh-CN/Kubernetes集群用户指南/授权管理/自定义RAM授权策略.md)。

    ```
    {
                "Action": [
                  "cs:ScaleCluster",
                  "cs:DeleteCluster"
                ],
                "Resource": "*",
                "Effect": "Allow"
             }
    ```

    ![修改权限](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2175659951/p44694.png)


## 子账号如何给其他子账号进行RBAC授权？

默认情况下，某子账号不具备对其他子账号进行RBAC授权的能力，需要首先确保某子账号在目标集群或命名空间上被授予预置的管理员角色或自定义中的cluster-admin角色，同时给某子账号授予RAM相应权限，策略内容包括：

-   查看其他RAM子账号
-   授予RAM权限策略
-   查看RBAC权限配置
-   RBAC授权能力

**说明：** 某子账号已在目标集群或命名空间上被授予预置的管理员角色或自定义中的cluster-admin角色。

1.  授予某子账号RAM权限。

    登录RAM控制台，给某子账号授予RAM权限，请参见[自定义RAM授权策略](/cn.zh-CN/Kubernetes集群用户指南/授权管理/自定义RAM授权策略.md)。

    授权策略内容示例参考：

    ```
    {
        "Statement": [{
                "Action": [
                    "ram:Get*",
                    "ram:List*",
                    "cs:GetUserPermissions",
                    "cs:GetSubUsers",
                    "cs:GrantPermission"
                ],
                "Resource": "*",
                "Effect": "Allow"
            },
            {
                "Action": [
                    "ram:AttachPolicyToUser",
                    "ram:AttachPolicy"
                ],
                "Effect": "Allow",
                "Resource":  [
                    "acs:ram:*:*:policy/xxxxxx",
                    "acs:*:*:*:user/*"
                ]
            }
        ],
        "Version": "1"
    }
                                
    ```

    **说明：** xxxxxx需要替换成您想要绑定的RAM策略名称。例如，您替换成\*，表示某子账号拥有所有RAM策略的授权绑定能力。

2.  某子账号给其他子账号授权。

    当某子账号完成了上述策略的绑定后，即拥有对其他子账号在指定策略范围内的RAM授权能力，以及在集群内的RBAC授权能力。您可以参见[配置子账号RBAC权限](/cn.zh-CN/Kubernetes集群用户指南/授权管理/配置子账号RBAC权限.md)，对其他子账号进行授权。


