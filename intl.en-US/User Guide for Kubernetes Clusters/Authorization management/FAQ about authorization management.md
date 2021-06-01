---
keyword: FAQ about authorization management
---

# FAQ about authorization management

This topic provides answers to some frequently asked questions about authorization management.

-   [Can I grant permissions on applications?](#section_7r6_eh8_lyb)
-   [How do I grant a RAM user the permissions to create clusters?](#section_wrw_5cm_wsj)
-   [How can I go to the RAM authorization page?](#section_fdh_k13_qyn)
-   [Why is a RAM user that has the cs:admin permission fail to create custom resource definitions \(CRDs\) in ACK?](#section_i8z_782_b6i)
-   [What do I do if the APISERVER\_403 error occurs?](#section_9ft_3qt_vjq)
-   [How do I reassign a RAM role to an ECS instance?](#section_q0q_oen_y5f)
-   [The RAM user is granted read-only permissions on all clusters but still fail to query all clusters](#section_x8g_crn_5ey)
-   [How do I assign custom RAM roles in an ACK cluster?](#section_0sm_z8q_eyo)
-   [How does a RAM user assign RBAC roles to other RAM users?](#section_wb5_8oc_wnd)
-   [How do I fix the "You have no permission to perform this operation. Contact the Alibaba Cloud account owner or an authorized RAM user to request permission." error?](~~142276~~)

## Can I grant permissions on applications?

Yes. You can grant permissions on applications. You can create a custom ClusterRole and define a rule to grant permissions on individual applications. You can use the `resourceNames` field to specify the applications.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Authorizations**.

3.  On the Authorizations page, select the RAM user that you want to manage on the Select RAM User wizard page and click **Modify Permissions**.

    **Note:** If you log on as a RAM user, make sure that the RAM user has at least read-only permissions on the cluster that you want to manage. In addition, the RAM user must be assigned the cluster-admin role or predefined RBAC administrator role. For more information, see [Create a custom RAM policy](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create a custom RAM policy.md).

4.  On the **Configure Role-Based Access Control \(RBAC\)** wizard page, click **![122](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6790951261/p261686.png)Add Permissions**. Select the cluster and namespace, and then select **Custom**. Select the ClusterRole that you want to manage from the **Custom** drop-down list and click **Next Step**.

    ![AN13](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8305851261/p261530.png)

    **Note:** You can assign one predefined RBAC role and one or more custom RBAC roles in the specified cluster and namespace to a RAM user.

    The following table describes the permissions that the predefined and custom RBAC roles have on clusters and namespaces.

    |Role|RBAC permissions on cluster resources|
    |----|-------------------------------------|
    |Administrator|Read and write permissions on resources in all namespaces.|
    |O&M Engineer|Read and write permissions on visible resources in the console in all namespaces and read-only permissions on nodes, persistent volumes \(PVs\), namespaces, and quotas.|
    |Developer|Read and write permissions on visible resources in the console in all or specified namespaces.|
    |Restricted User|Read-only permissions on visible resources in the console in all or specified namespaces.|
    |Custom|The permissions of a custom role are determined by the ClusterRole that you select. Before you select a ClusterRole, check the permissions of the ClusterRole and make sure that you grant only the required permissions to the RAM user. For more information, see [Create a custom RAM policy](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create a custom RAM policy.mdsection_5ya_j6r_fh7).|

    For more information about the subsequent steps, see [Assign RBAC roles to a RAM user](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Assign RBAC roles to a RAM user.md).


## How do I grant a RAM user the permissions to create clusters?

1.  Use your Alibaba Cloud account to grant permissions to the system roles used by Container Service for Kubernetes \(ACK\).

    -   You need only to grant permissions to the system roles once. If you are not sure whether the permissions are granted, log on using your Alibaba Cloud account and see: [https://ur.alipay.com/1paTcxSWdAEW70GVH5TZiO](https://ur.alipay.com/1paTcxSWdAEW70GVH5TZiO).
    -   For more information about the default system roles, see [ACK default roles](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/ACK default roles.md).
2.  Use your Alibaba Cloud account to assign custom RAM policies to the RAM user.

    Make sure that the RAM user has the `cs:CreateCluster` permission. For more information, see [Create a custom RAM policy](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create a custom RAM policy.md).

    The following YAML template is provided as an example.

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

    -   When you create a cluster, you must assign cloud resources such as virtual private cloud \(VPC\) resources to the cluster. Make sure that the RAM user is granted the required permissions to access cloud resources.
    -   Make sure that the RAM user has the List permission on VPC resources. To grant this permission, you can attach the AliyunVPCReadOnlyAccess policy to the RAM user.
    -   If you want to configure other resources for the cluster, check the corresponding permission policy or documents on authorization. For more information, see [RAM authorization](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Overview.md).

## How can I go to the RAM authorization page?

If you revoked the permissions granted to the system roles used by ACK, you must grant the permissions again.

For more information, see [Step 1: Assign the default roles](/intl.en-US/Quick Start/Quick start for first-time users.md).

**Note:** You must use your Alibaba Cloud account to grant the permissions.

## Why is a RAM user that has the `cs:admin` permission fail to create custom resource definitions \(CRDs\) in ACK?

The default administrator of clusters that are created before May 2019 does not have access permissions on all Kubernetes resources. To fix this issue, you can assign a custom `cluster-admin` role to the RAM user, or delete the existing `cs:admin` ClusterRole and create a ClusterRole.

The following YAML template is provided as an example.

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

The current RAM user is not granted the required RBAC permissions on the Kubernetes cluster. You must go to the **Authorizations** page to grant permissions to the RAM user. For more information, see [Assign RBAC roles to a RAM user](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Assign RBAC roles to a RAM user.md).

## How do I reassign a RAM role to an ECS instance?

When the application that runs on an Elastic Compute Service \(ECS\) instance sends requests to `metadata api 100`, the **404** error code or the following error message is returned: **Message:Node condition RAMRoleError is now: True, reason: NodeHasNoRAMRole**. You can reassign a RAM role to an ECS instance by using the following methods:

-   If the RAM role of an ECS instance is deleted, you must reassign the RAM role of the corresponding node to the ECS instance. For more information, see [Replace an instance RAM role](/intl.en-US/Security/Instance RAM roles/Manage an instance RAM role/Replace an instance RAM role.md).
    -   If the ECS instance serves as a master node in your cluster, you must assign the master role to the ECS instance. Choose **Cluster Information** \> **Cluster Resources** \> **Master RAM Role**.
    -   If the ECS instance serves as a worker node in your cluster, you must assign the worker role to the ECS instance. Choose **Cluster Information** \> **Cluster Resources** \> **Worker RAM Role**.
-   If you modified the policies of the RAM role, check whether the modified content is valid.
-   If you modified the policies of the RAM role before errors occurred, try to roll back the policies to the original version.

## The RAM user is granted read-only permissions on all clusters but still fail to query all clusters

**Problem**

The RAM user is granted read-only permissions on all clusters by using the RAM console, and access permissions on specified namespaces of two clusters by using RBAC. Previously, the RAM user can query all clusters in the console. However, the RAM user can query only some of the clusters now. The permissions of the RAM user are not recently modified.

**Cause**

You did not log on to the console by using the RAM user that has read-only permissions on all clusters. Alternatively, you did not select to display **All Resources** in the console.

**Solution**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the top navigation bar, choose **All Resources** \> **All Resources**.

    ![11](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7790951261/p259620.png)

3.  Move the pointer over the avatar in the upper-right corner and make sure that you are logged on as the RAM user.


## How do I assign custom RAM roles in an ACK cluster?

You cannot assign custom RAM roles in an ACK cluster. However, you can attach custom permission policies to the worker RAM role that is automatically created when you create worker nodes in the cluster.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the **details** page, click **Cluster Information**.

5.  On the Cluster Information page, click the **Cluster Resources** tab and click the link to the right of **Worker RAM Role**.

6.  You are redirected to the RAM console. On the Permissions tab, click the name in the Policy column.

7.  On the details page of the policy, click **Modify Policy Document**. In the Modify Policy Document pane, paste the following content into the Policy Document code editor and click **OK**. In this example, the permissions to scale and delete clusters are added to the policy. For more information, see [Create a custom RAM policy](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create a custom RAM policy.md).

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

By default, you can use only an Alibaba Cloud account to assign RBAC roles to other RAM users. To allow a RAM user to assign RBAC roles to other RAM users, you must first assign the predefined RBAC administrator role or cluster-admin role to the RAM user. This way, the RAM user has the permissions to manage the cluster or namespace. In addition, you must attach a RAM permission policy to the RAM user. The permission policy must contain the following content:

-   The permissions to view other RAM users that belong to the current Alibaba Cloud account.
-   The permissions to attach permission policies to other RAM users.
-   The permissions to view configurations of RBAC roles.
-   The permissions to assign RBAC roles to other RAM users.

1.  Grant RAM permissions to the RAM user:

    Log on to the [RAM console](https://ram.console.aliyun.com/) and grant RAM permissions to the RAM user. For more information, see [Create a custom RAM policy](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create a custom RAM policy.md).

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

    **Note:** Replace xxxxxx with the name of the permission policy that you want to allow the RAM user to attach to other RAM users. If you replace xxxxxx with an asterisk \(\*\), it indicates that the RAM user is authorized to attach all permission policies to other RAM users.

2.  Use the RAM user to grant permissions to other RAM users.

    After the RAM user is attached with the preceding policy, the RAM user is authorized to attach the specified permission policies to other RAM users and assign RBAC roles to other RAM users. For more information about how to assign RBAC roles to a RAM user, see [Assign RBAC roles to a RAM user](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Assign RBAC roles to a RAM user.md).


