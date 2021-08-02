---
keyword: [RAM user, RBAC permission, ]
---

# Assign RBAC roles to RAM users

Role-based access control \(RBAC\) controls network access based on the roles of users. You can associate cluster roles with multiple permissions and configure different permission policies for different roles. This topic describes how to assign RBAC roles to Resource Access Management \(RAM\) users.

## Configuration notes

-   By default, a RAM user does not have access permissions on the Kubernetes resources in a cluster if the cluster is not created by the RAM user.
-   To assign RBAC roles to a RAM user, you must ensure that the RAM user is granted at least read-only permissions on the specified cluster in the [RAM console](https://ram.console.aliyun.com/).
-   Container Service for Kubernetes \(ACK\) allows you to assign the following predefined RBAC roles to RAM users: Administrator, O&M Engineer, Developer, Restricted User, and Custom. The Administrator role has access permissions on all Kubernetes resources in the cluster.
-   If a RAM user assumes the Administrator role, the RAM user can grant other RAM users all cluster-scoped permissions. Newly created clusters are automatically bound to existing ClusterRoles.
-   When a RAM user assumes the O&M Engineer role to authorize other RAM users, only the clusters and namespaces that the RAM user is authorized to manage are listed in the console. In addition, the RAM user must be assigned the Administrator or cluster-admin role of the specified cluster or namespace. Otherwise, the RAM user cannot authorize other RAM users to access the specified cluster or namespace.
-   ACK allows you to assign RBAC roles to multiple RAM users at a time.
-   To ensure data security, you are not allowed to modify RAM permission policies that are attached to your RAM users in the ACK console. You must read the instructions on the authorization page, log on to the RAM console, and then modify the permission policies.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Authorizations** to go to the Authorizations page.

3.  On the Authorizations page, click the RAM Users tab. Find the RAM user to which you want to grant permissions and click **Modify Permissions** to go to the Configure Role-Based Access Control \(RBAC\) wizard page.

    **Note:** If you log on to the ACK console as a RAM user, make sure that the RAM user has at least read-only permissions on the cluster that you want to manage. In addition, the RAM user must be assigned the cluster-admin role or Administrator role of the cluster. For more information, see [Create a custom RAM policy](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create a custom RAM policy.md).

4.  On the **Configure Role-Based Access Control \(RBAC\)** wizard page, click the plus sign \(+\) to add cluster-scoped or namespace-scoped permissions and select a predefined or custom RBAC role in the Permission column. You can also click the minus sign \(-\) to delete permissions. After you add the permissions, click **Next Step**.

    **Note:** You can assign one predefined RBAC role and one or more custom RBAC roles of the specified cluster or namespace to a RAM user.

    ![Assign RBAC roles](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/1235359951/p10618.png)

    The following table describes the permissions that the predefined and custom RBAC roles have on clusters and namespaces.

    |Role|RBAC permissions on cluster resources|
    |----|-------------------------------------|
    |Administrator|Read and write permissions on resources in all namespaces.|
    |O&M Engineer|Read and write permissions on visible resources in the console in all namespaces and read-only permissions on nodes, persistent volumes \(PVs\), namespaces, and quotas.|
    |Developer|Read and write permissions on visible resources in the console in all or specified namespaces.|
    |Restricted User|Read-only permissions on visible resources in the console in all or specified namespaces.|
    |Custom|The permissions of a custom role are determined by the ClusterRole that you select. Before you select a ClusterRole, check the permissions of the ClusterRole and make sure that you grant only the required permissions to the RAM user. For more information, see [Create a custom RAM policy](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create a custom RAM policy.md).|

5.  On the **Submit Authorization** wizard page, if **The authorization is complete** appears, it indicates that the RBAC roles are assigned to the RAM user. If the result in the following [Figure 1](#fig_554_7qb_33s) figure is returned, it indicates that the RBAC roles are not assigned because the RAM user is unauthorized to access the cluster. You must read the instructions on the returned page, log on to the RAM console, and then grant the RAM user at least read-only permissions on the cluster.

    1.  On the Submit Authorization wizard page, click **Copy** and then click **Policy Management** to go to the RAM console.

        ![Authorizations](../images/p44121.png "Authorizations")

    2.  In the left-side navigation pane, choose **Permissions** \> **Policies**. On the Policies page, click **Create Policy**.

        ![Create a permission policy](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/2235359951/p44133.png)

    3.  On the Create Custom Policy page, specify **Policy Name** and set Configuration Mode to **Script**. Then, press **Ctrl+V** to paste the policy content that was copied in [Step 5.a](#substep_yhu_px3_las) to the **Policy Document** section and then click **OK**. For more information, see [Create a custom RAM policy](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create a custom RAM policy.md).

        ![Create a custom permission policy](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/2235359951/p44122.png)

    4.  In the left-side navigation pane, choose **Identities** \> **Users**. On the Users page, find the RAM user to which you want to grant permissions and click **Add Permissions** in the Actions column.

        ![Add permissions](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/2235359951/p44124.png)

    5.  In the Add Permissions pane, specify **Authorized Scope**, select **Custom Policy**, and then search for the policy that was created in Step 5.c. Click the name of the policy to move the policy to the **Selected** section on the right side of the page. Click **OK**. This grants the RAM user read-only permissions on the specified cluster.

        ![Authorization](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/2235359951/p44125.png)

    6.  Return to the ACK console. On the Submit Authorization wizard page, click **Submit Authorization**. The RBAC roles are assigned to the RAM user.

6.  After the RAM user is authorized, you can log on to the ACK console as the authorized RAM user to manage the specified cluster.


## Predefined and custom RBAC roles

ACK provides the following predefined RBAC roles: Administrator, O&M Engineer, Developer, and Restricted User. You can use these roles to regulate access to the ACK console in most scenarios. In addition, you can use custom roles to control permissions on clusters.

ACK provides a set of custom RBAC roles.

**Note:** The cluster-admin role is similar to a super administrator. By default, the cluster-admin role has the permissions to manage all resources within a cluster.

![Custom RBAC roles](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/2235359951/p14375.png)

You can log on to a master node of a cluster and run the following command to query the custom RBAC roles that are assigned to the current account:

`kubectl get clusterrole`

```
kubectl get clusterrole
NAME                                                                   AGE
admin                                                                  13d
alibaba-log-controller                                                 13d
alicloud-disk-controller-runner                                        13d
cluster-admin                                                          13d
cs:admin                                                               13d
edit                                                                   13d
flannel                                                                13d
kube-state-metrics                                                     22h
node-exporter                                                          22h
prometheus-k8s                                                         22h
prometheus-operator                                                    22h
system:aggregate-to-admin                                              13d
....  
system:volume-scheduler                                                13d
view                                                                   13d
            
```

Run the following command to query the details of a role, for example, the cluster-admin role:

`kubectl get clusterrole cluster-admin -o yaml`

**Note:** After a RAM user is assigned the cluster-admin role, the RAM user has the same permissions as the Alibaba Cloud account to which the RAM user belongs. The RAM user has full control over all resources within the cluster. Proceed with caution.

```
kubectl get clusterrole cluster-admin  -o yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  annotations:
    rbac.authorization.kubernetes.io/autoupdate: "true"
  creationTimestamp: 2018-10-12T08:31:15Z
  labels:
    kubernetes.io/bootstrapping: rbac-defaults
  name: cluster-admin
  resourceVersion: "57"
  selfLink: /apis/rbac.authorization.k8s.io/v1/clusterroles/cluster-admin
  uid: 2f29f9c5-cdf9-11e8-84bf-00163e0b2f97
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

