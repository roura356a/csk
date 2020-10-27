# FAQ about authorization

This topic provides answers to some commonly asked questions about authorization.

-   [How to assign custom RAM roles to ACK clusters?](#section_0sm_z8q_eyo)
-   [How to use a RAM user to assign RBAC roles to other RAM users?](#section_wb5_8oc_wnd)

## How to assign custom RAM roles to ACK clusters?

You cannot assign custom Resource Access Management \(RAM\) roles to clusters of Container Service for Kubernetes \(ACK\). However, you can attach custom permission policies to the worker role that is automatically created by the system when the system creates worker nodes.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the **details** page, click **Cluster Information**.

5.  On the Cluster Information page, click the **Cluster Resources** tab and click the name of the **worker RAM role** on the tab.

6.  You are then redirected to the RAM console. On the Permissions tab, click the name in the Policy column.

7.  On the details page of the policy, click **Modify Policy Document**. On the Modify Policy Document pane, paste the following code block into the Policy Document code editor and click **OK**. In this example, the permissions to scale and delete clusters are added to the policy. For more information, see [Customize RAM permission policies](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create custom RAM permission policies.mdtable_pzw_5s2_xdb).

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

    ![Modify permissions](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3235359951/p44694.png)


## How to use a RAM user to assign RBAC roles to other RAM users?

By default, you can use only an Alibaba Cloud account to assign RBAC roles to other RAM users. To use a RAM user to assign RBAC roles to other RAM users, you must first assign the predefined RBAC administrator role or cluster-admin role to the RAM user. This way, the RAM user has permissions to manage the cluster or namespace that you want other RAM users to manage. In addition, you must attach a RAM permission policy to the RAM user. The permission policy must contain the following content:

-   The permissions to view other RAM users under the current Alibaba Cloud account.
-   The permissions to attach permission policies to other RAM users.
-   The permissions to view configurations of RBAC roles.
-   The permissions to assign RBAC roles to other RAM users.

**Note:** You must assign the predefined RBAC administrator role or the cluster-admin role to the RAM user so that the RAM user can manage the corresponding cluster or namespace.

1.  Grant the required permissions to the RAM user.

    Log on to the RAM console and attach a permission policy that contains the preceding permissions to the RAM user. For more information, see [t16637.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create custom RAM permission policies.md).

    The following is an example of the policy content:

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

    **Note:** Replace xxxxxx with the name of the permission policy that you want to attach to other RAM users. If you replace with an asterisk \(\*\), it indicates that the RAM user is authorized to attach all permission policies to other RAM users.

2.  Use the RAM user to authorize other RAM users.

    After the RAM user is attached with the preceding permission policy, the RAM user is authorized to assign RBAC roles to other RAM users and grant them limited permissions. For more information about how to assign RBAC roles to a RAM user, see [t17456.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Assign RBAC roles to a RAM user.md).


