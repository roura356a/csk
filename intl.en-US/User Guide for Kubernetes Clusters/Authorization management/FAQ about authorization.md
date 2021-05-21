---
keyword: FAQ about authorization
---

# FAQ about authorization

This topic provides answers to some frequently asked questions about authorization.

-   [Can I grant a RAM user permissions on an application?](#section_7r6_eh8_lyb)
-   [How do I grant a RAM user the permissions to create clusters?](#section_wrw_5cm_wsj)
-   [How do I reauthorize ACK?](#section_fdh_k13_qyn)
-   [Why is a RAM user that has the cs:admin permission not allowed to create custom resource definitions \(CRDs\) in ACK?](#section_i8z_782_b6i)
-   [What do I do if the APISERVER\_403 error occurs?](#section_9ft_3qt_vjq)
-   [How do I reassign a RAM role to an ECS instance?](#section_q0q_oen_y5f)
-   [Why am I unable to view specific clusters after I grant a RAM user read-only permissions on all clusters?](#section_x8g_crn_5ey)
-   [How do I assign custom RAM roles to ACK clusters?](#section_0sm_z8q_eyo)
-   [How does a RAM user assign RBAC roles to other RAM users?](#section_wb5_8oc_wnd)

## Can I grant a RAM user permissions on an application?

Yes. To grant a RAM user permissions on an application, you can customize a ClusterRole policy, specify the name of the application in the `resourceNames` field, and then attach the policy to the RAM user.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Authorizations**.

3.  On the Authorizations page, select the RAM user that you want to manage on the Select RAM User wizard page and click **Modify Permissions**.

    **Note:** If you log on to the Container Service for Kubernetes \(ACK\) console as a RAM user, make sure that the RAM user has read-only permissions on the cluster that you want to manage. You must also assign the cluster-admin role or predefined administrator role in role-based access control \(RBAC\) to the RAM user. For more information, see [Customize permission policies](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create a custom RAM policy.md)

4.  On the **Configure Role-Based Access Control \(RBAC\)** wizard page, click **![122](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6790951261/p261686.png)Add Permissions**. Select the required cluster and namespace and click **Custom** in the Permission column. Select a ClusterRole policy from the **Custom** drop-down list and click **Next Step**.

    ![AN13](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8305851261/p261530.png)

    **Note:** For each RAM user, you can assign only one predefined role but one or more custom roles to manage the same cluster or namespace.

    The following table describes the permissions that the predefined roles have on clusters and namespaces.

    |Role|Permissions on cluster resources|
    |----|--------------------------------|
    |Administrator|Read and write permissions on resources in all namespaces.|
    |O&M engineer|Read and write permissions on visible resources in all namespaces in the ACK console and read-only permissions on nodes, persistent volumes \(PVs\), namespaces, and quotas.|
    |Developer|Read and write permissions on visible resources in all namespaces or the specified namespace in the ACK console.|
    |Restricted user|Read-only permissions on visible resources in all namespaces or the specified namespace in the ACK console.|
    |Custom|The permissions of the RAM user are determined by the ClusterRole policy that you select. Before you select a ClusterRole policy, check the permissions that the policy grants and make sure that you grant only the required permissions to the RAM user. For more information, see [Customize permission policies](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create a custom RAM policy.mdsection_5ya_j6r_fh7).|

    For more information, see [Assign RBAC roles to a RAM user](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Assign RBAC roles to a RAM user.md).


## How do I grant a RAM user the permissions to create clusters?

1.  Use your Alibaba Cloud account to assign the default roles in batches.

    -   You need to assign the default roles only once. If you are not sure whether the default roles are assigned to a RAM user, log on with your Alibaba Cloud account and visit the following link to assign the default roles to the RAM user in batches: [https://ur.alipay.com/1paTcxSWdAEW70GVH5TZiO](https://ur.alipay.com/1paTcxSWdAEW70GVH5TZiO).
    -   For more information about the default roles of ACK, see [ACK default roles](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/ACK default roles.md).
2.  Use your Alibaba Cloud account to attach a custom permission policy to the RAM user.

    Make sure that the RAM user is granted the `cs:CreateCluster` permission. For more information, see [Create a custom RAM policy](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create a custom RAM policy.md).

    The following YAML template is an example:

    ```
    {
     "Statement": [{
         "Action": [
             "cs:*"
         ],
         "Effect": "Allow",
         "Resource": [
             "CreateCluster"
         ]
     }],
     "Version": "1"
    }
    ```

    **Note:**

    -   You must specify a virtual private cloud \(VPC\) when you create a cluster. Therefore, you must authorize the RAM user to access VPC resources.
    -   Make sure that the RAM user is granted the List permission on VPC resources. To grant this permission, you can attach the AliyunVPCReadOnlyAccess permission policy to the RAM user.
    -   If the RAM user wants to access other cloud resources, attach the relevant system permission policy or follow the steps in the corresponding topic. For more information, see [RAM authorization](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Overview.md).

## How do I reauthorize ACK?

If the authorization of ACK becomes invalid, you must reauthorize ACK.

For more information, see [Step 1: Assign the default roles](/intl.en-US/Quick Start/Quick start for first-time users.md).

**Note:** You must use your Alibaba Cloud account to reauthorize ACK.

## Why is a RAM user that has the `cs:admin` permission not allowed to create custom resource definitions \(CRDs\) in ACK?

If your cluster was created before May 2019, the default administrator role of the cluster is not allowed to access some ACK resources. You can assign the `cluster-admin` role to the RAM user. You can also delete the ClusterRole policy that contains the `cs:admin` permission and then recreate the ClusterRole policy.

The following YAML template is an example:

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cs:admin
rules:
- apiGroups:
  - '*'
  resources:
  - '*'
  verbs:
  - '*'
- nonResourceURLs:
  - '*'
  verbs:
  - '*'
```

## What do I do if the APISERVER\_403 error occurs?

This error occurs because the RAM user is not assigned the required RBAC roles. You can go to the **Authorizations** page to authorize the RAM user. For more information, see [Assign RBAC roles to a RAM user](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Assign RBAC roles to a RAM user.md).

## How do I reassign a RAM role to an ECS instance?

When the application that runs on an Elastic Compute Service \(ECS\) instance sends requests to `metadata api 100`, the **404** error code or the following error message is returned: **Message:Node condition RAMRoleError is now: True, reason: NodeHasNoRAMRole**. You can reassign a RAM role to an ECS instance by using the following methods:

-   If the RAM role of the ECS instance is deleted, you must reassign the RAM role of the corresponding node to the ECS instance. For more information, see [Replace an instance RAM role](/intl.en-US/Security/Instance RAM roles/Manage an instance RAM role/Replace an instance RAM role.md).
    -   If the ECS instance serves as a master node in your cluster, you must assign the master role to the ECS instance. Choose **Cluster Information** \> **Cluster Resources** \> **Master RAM Role**.
    -   If the ECS instance serves as a worker node in your cluster, you must assign the worker role to the ECS instance. Choose **Cluster Information** \> **Cluster Resources** \> **Worker RAM Role**.
-   If you have modified the permission policy attached to the RAM role, check whether the modified permission policy is valid.
-   If you have modified the permission policy that is attached to the RAM role before the error occurs, undo all changes.

## Why am I unable to view specific clusters after I grant a RAM user read-only permissions on all clusters?

**Symptom**

A RAM user is granted read-only permissions on all clusters in the RAM console. The RAM user can assume RBAC roles to access specific namespaces in the two specified clusters. The RAM user can view all clusters in the ACK console before but can view only some clusters at present. The permissions of the RAM user are valid and no changes are made.

**Cause**

The RAM user that logs on to the ACK console is different from the required RAM user or a specific resource group is selected. Switch to the required RAM user and select **All Resources** in the top navigation bar of the ACK console.

**Solution**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the top navigation bar, choose **All Resources** \> **All Resources**.

    ![11](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7790951261/p259620.png)

3.  Move the pointer over the avatar in the upper-right corner of the page to check the current account that logs on to the console. Make sure that the required RAM user logs on to the console.


## How do I assign custom RAM roles to ACK clusters?

You cannot assign custom RAM roles to ACK clusters. However, you can attach custom policies to the worker role that is automatically created by the system when the system creates worker nodes for a cluster.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the **details** page, click the **Cluster Resources** tab.

5.  On the **Cluster Resources** tab, click the link on the right side of **Worker RAM Role**.

6.  You are redirected to the RAM console. On the RAM roles tab, click the name of a permission policy in the Policy column.

7.  On the Policies tab, click **Modify Policy Document**. In the Modify Policy Document panel, copy the following code block into the Policy Document code editor and click **OK**. In this example, the permissions to scale and delete clusters are added to the policy. For more information, see [Create a custom RAM policy](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create a custom RAM policy.md).

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

    ![Modify permissions](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3235359951/p44694.png)


## How does a RAM user assign RBAC roles to other RAM users?

By default, you must use Alibaba Cloud account to assign RBAC roles to RAM users. To allow a RAM user to assign RBAC roles to other RAM users, you must first assign the predefined administrator role in RBAC or cluster-admin role to the RAM user. This way, the RAM user has permissions to manage clusters or namespaces. You must also attach a permission policy that grants the following permissions to the RAM user:

-   The permissions to view other RAM users that belong to the current Alibaba Cloud account.
-   The permissions to attach permission policies to other RAM users.
-   The permissions to view configurations of RBAC roles.
-   The permissions to assign RBAC roles to other RAM users.

1.  Grant permissions to the RAM user.

    Log on to the [RAM console](https://ram.console.aliyun.com/) and grant permissions to the RAM user. For more information, see [Create a custom RAM policy](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create a custom RAM policy.md).

    Example:

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

    **Note:** Replace xxxxxx with the name of the permission policy that you want to attach to other RAM users. If you replace xxxxxx with an asterisk \(\*\), the RAM user is authorized to attach all permission policies to other RAM users.

2.  Use the RAM user to grant permissions to other RAM users.

    After you attach the preceding permission policy to the RAM user, the RAM user is authorized to assign RBAC roles and grant permissions to other RAM users. For more information about how to assign RBAC roles to a RAM user, see [Assign RBAC roles to a RAM user](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Assign RBAC roles to a RAM user.md).


