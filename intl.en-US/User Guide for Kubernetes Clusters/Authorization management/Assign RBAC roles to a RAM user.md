---
keyword: [RAM users, RBAC permissions]
---

# Assign RBAC roles to a RAM user

This topic describes how to assign role-based access control \(RBAC\) roles to Resource Access Management \(RAM\) users. Different RBAC roles have different permissions on clusters of Container Service for Kubernetes \(ACK\) and namespaces.

## Prerequisites

-   An Alibaba Cloud account is created and one or more RAM users are created under the Alibaba Cloud account.
-   The current account \(Alibaba Cloud account or RAM user\) is granted at least read-only permissions on specified ACK clusters.
-   RAM authorization regulates cluster-level access control. You can grant read and write permissions on the clusters that you want to manage in the RAM console.

    -   Read-only policy: grants read-only permissions on specified clusters, for example, the permissions to view the configurations and kubeconfig file of a cluster.
    -   Read-write policy: grants read and write permissions on specified clusters, for example, the permissions to scale, upgrade, delete, and add nodes.
    For more information, see [Customize RAM permission policies](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create custom RAM permission policies.md).

-   If you assign the cluster-admin role or the predefined RBAC administrator role to a RAM user that has read-only permissions on a cluster or namespace, the RAM user can assign RBAC roles to other RAM users. RAM users that are assigned RBAC roles have limited permissions to manage the same cluster or namespace. For more information, see [Customize RAM permission policies](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create custom RAM permission policies.md).
-   To ensure data security, you are not allowed to modify RAM permission policies that are attached to your RAM users in the ACK console. You must read the instructions on the authorization page, log on to the RAM console, and modify the permission policies accordingly.

## Announcements

To ensure data security, ACK has adjusted the strategy on cluster access control.

-   Unauthorized RAM users are not allowed to access cluster resources. You can authorize your RAM users to access clusters and cloud resources in the RAM console.
-   RAM users are granted limited permissions only on clusters within the authorization realm. RAM users are not allowed to access clusters outside the authorization realm in compatibility mode.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Authorizations** to go to the Authorizations page.

3.  On the Authorizations page, click the RAM Users tab. On the RAM Users tab, find the RAM user to which you want to grant permissions and click **Modify Permissions** to open the Configure Role-Based Access Control \(RBAC\) wizard page.

    **Note:** If you log on to the ACK console as a RAM user, make sure that the RAM user has at least read-only permissions on the cluster you want to manage and is assigned the cluster-admin role or the predefined RBAC administrator role. For more information, see [Customize RAM permissions](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create custom RAM permission policies.md)

4.  On the **Configure Role-Based Access Control \(RBAC\)** wizard page, click the plus sign \(+\) to add cluster-scoped or namespace-scoped permissions and select a predefined or custom RBAC role in the Permission column. You can also click the minus sign \(-\) to delete permissions. Click **Next Step**.

    **Note:** For each RAM user, you can assign only one predefined RBAC role but one or more custom RBAC roles to manage the same cluster or namespace.

    ![Assign RBAC roles](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1235359951/p10618.png)

    The following table describes permissions that the predefined and custom RBAC roles have on clusters and namespaces.

    |Role|Cluster-scoped and namespace-scoped permission|
    |----|----------------------------------------------|
    |Administrator|Read and write permissions on resources in all namespaces.|
    |O&M Engineer|Read and write permissions on resources in all namespaces and read-only permissions on nodes, persistent volumes \(PVs\), namespaces, and service quotas within a cluster.|
    |Developer|Read and write permissions on resources in a specified namespace or all namespaces.|
    |Restricted User|Read-only permissions on resources in a specified namespace or all namespaces.|
    |Custom|The cluster role that you select for a custom role determines what permissions the custom role has. Before you select a cluster role, make sure that you are aware of the permissions that the cluster role has in case the RAM user is granted excessive permissions. For more information, see [Customize RAM permission policies](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create custom RAM permission policies.md).|

5.  On the **Submit Authorization** wizard page, if **The authorization is complete** is returned, it indicates that the RBAC roles are assigned to the RAM user. If the result in the following [Figure 1](#fig_554_7qb_33s) figure is returned, it indicates that RBAC roles are not assigned because the RAM user is not authorized to access the cluster. You must read the instructions on the authorization page, log on to the RAM console, and grant the RAM user at least read-only permissions on the cluster. For more information, see [Customize RAM permission policies](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create custom RAM permission policies.md).

    1.  On the Submit Authorization wizard page, click **Copy** and then click **policy management** to go to the RAM console.

        ![Authorizations](../images/p44121.png "Authorizations")

    2.  In the left-side navigation pane, choose **Permissions** \> **Policies**. On the Policies page, click **Create Policy**.

        ![Create a policy](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2235359951/p44133.png)

    3.  On the Create Custom Policy page, enter a **name** for the policy and set Configuration Mode to **Script**. Then, press **Ctrl+V** to paste the policy content that is copied in Step 5.a to the **Policy Document** section, and click **OK**.

        ![Create custom policy](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2235359951/p44122.png)

    4.  In the left-side navigation pane of the RAM console, choose **Identities** \> **Users**. On the Users page, find the RAM user to which you want to grant permissions and click **Add Permissions** in the Actions column.

        ![Grant permissions](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2235359951/p44124.png)

    5.  In the Add Permissions pane, set **Authorization**, select **Custom Policy**, and find and click the name of the policy that is created in Step 5.c. The policy is added to the **Selected** section on the right side of the page. Click **OK**. Read-only permissions on the cluster are granted to the RAM user.

        ![Authorization](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2235359951/p44125.png)

    6.  Return to the ACK console. On the Submit Authorization wizard page, click **Submit Authorization**. The RBAC roles are assigned to the RAM user.

6.  After the authorization is complete, you can log to the ACK console as the authorized RAM user and perform the allowed operations to manage ACK.


## Predefined and custom RBAC roles

ACK provides the following predefined RBAC roles: administrator, O&M engineer, developer, and restricted user. You can use these roles to regulate ACK access control in most scenarios. In addition, you can use custom roles to customize permissions on clusters.

ACK provides a set of custom RBAC roles.

**Note:** The cluster-admin role is similar to a super administrator. By default, the cluster-admin role has the permissions to manage all resources within a cluster.

![Custom RBAC roles](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2235359951/p14375.png)

You can log on to a master node of a cluster and run the following command to view the custom RBAC roles that are assigned to the current account.

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

Run the following command to view the details of a role, for example, the cluster-admin role:

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

