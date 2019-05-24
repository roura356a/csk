# 子账号如何给其他子账号进行RBAC授权 {#concept_266170 .concept}

本文主要介绍子账号如何给其他子账号进行RBAC授权。

## 背景信息 {#section_4mu_y5u_1uc .section}

默认情况下，某子账号不具备对其他子账号进行RBAC授权的能力，需要首先确保某子账号在目标集群或命名空间上被授予预置的管理员角色或自定义中的cluster-admin角色，同时给某子账号授予RAM相应权限，策略内容包括：

-   查看其他RAM子账号
-   授予RAM权限策略
-   查看RBAC权限配置
-   RBAC授权能力

## 前提条件 {#section_akt_7a7_2vq .section}

某子账号已在目标集群或命名空间上被授予预置的管理员角色或自定义中的cluster-admin角色。

## 操作步骤 {#section_7o9_lap_zbq .section}

1.  授予某子账号RAM权限。

    登录RAM控制台，给某子账号授予RAM权限，请参见[自定义RAM授权策略](../../../../intl.zh-CN/用户指南/Kubernetes集群/授权管理/自定义RAM授权策略.md#)。

    授权策略内容示例参考：

    ``` {#codeblock_hy5_mff_bed}
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

    当某子账号完成了上述策略的绑定后，即拥有对其他子账号在指定策略范围内的RAM授权能力，以及在集群内的RBAC授权能力。您可以参考[子账号RBAC权限配置指导](../../../../intl.zh-CN/用户指南/Kubernetes集群/授权管理/子账号RBAC权限配置指导.md#)，对其他子账号进行授权。


