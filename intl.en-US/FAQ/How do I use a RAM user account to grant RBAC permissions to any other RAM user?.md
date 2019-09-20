# How do I use a RAM user account to grant RBAC permissions to any other RAM user? {#concept_1392440 .concept}

To grant RBAC permissions to any other RAM user, follow these steps:

1.  Set the preset role `Administrator` or the custom role `cluster-admin` for your RAM user account in the target Kubernetes cluster or namespace, and grant corresponding RAM permissions to your RAM user account.

    **说明：** The following RAM permissions are required:

    -   View any other RAM user accounts.
    -   Grant RAM permissions to any other RAM user accounts.
    -   View RBAC permissions that are granted to any other RAM user accounts.
    -   Grant RBAC permissions to any other RAM user accounts.
2.  Use your RAM user account to log on to the RAM console to grant RAM permissions to the target RAM user. For more information, see [Create custom authorization policies](../../../../reseller.en-US/User Guide for Kubernetes Clusters/Authorization management/Create custom authorization policies.md#).

    The following is a sample policy of RAM permissions:

    ``` {#codeblock_jnc_o20_kyn}
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

    **说明：** xxxxxx indicates the name of a RAM permission policy. For example, if you replace xxxxxx with \*, the target RAM user account then can grant all the RAM permissions to any other RAM users.

3.  Use your RAM user account to grant RBAC permissions to the target RAM user. For more information, see [Grant RBAC permissions to a RAM user](../../../../reseller.en-US/User Guide for Kubernetes Clusters/Authorization management/Grant RBAC permissions to a RAM user.md#).

