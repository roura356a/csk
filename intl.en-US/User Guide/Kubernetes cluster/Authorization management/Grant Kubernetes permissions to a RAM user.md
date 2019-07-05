# Grant Kubernetes permissions to a RAM user {#concept_qlf_lv4_f2b .concept}

This topic describes how to configure Kubernetes Resource Access Management \(RAM\) cluster permissions and the corresponding Kubernetes RBAC application permissions within the cluster by using the Container Service console.

## Before you begin {#section_ktd_rvn_42b .section}

-   Make sure that you have an Alibaba Cloud account and that you have created one or more RAM users.
-   If you grant a RAM user the corresponding permissions for a Container Service Kubernetes cluster, you must use an account to complete the task if further operations in the RAM console is required.
-   If a RAM user is granted the preset admin role or the custom cluster-admin role in a cluster or namespace, then the RAM user can grant permissions to other RAM users in the same cluster or namespace.
-   If RAM authorization is involved in configuring permissions through the Container Service console, you must manually perform authorization in the RAM console for the target RAM user according to the reference policy and operation instructions on the page due the security restrictions of RAM.

## Authorization policy upgrade notice {#section_q1y_pv4_f2b .section}

Container Service will upgrade the cluster authorization policy \(in April 2019\) to enhance the security of Kubernetes clusters. After the upgrade, any RAM users in a Kubernetes cluster that are not granted the required permissions will not be able to access the cluster resources.

Therefore, we recommend that you grant required permissions to the RAM users in each of your Kubernetes clusters. After you complete this process, your managed RAM users will only have the specified permissions to access the their corresponding authorized cluster.

## Procedure {#section_ot2_4v4_f2b .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Container Service - Kubernetes, choose **Clusters** \> **Authorization**.
3.  On the right of the target RAM user, click **Authorize**.

    **Note:** If you want to use a RAM user for the preceding operations, make sure that the target RAM user has been granted the corresponding admin permissions or the cluster-admin role.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17456/156231059810587_en-US.png)

4.  In the upper-left corner of the Resource Authorization tab page, click the plus sign, set the cluster and namespace where the permissions to be granted apply, set a role for the RAM user, and then click **Next Step**.

    **Note:** 

    -   You can grant one preset role and multiple custom roles to a RAM user in a cluster or a namespace.
    -   You can click the minus sign to remove a group of permission settings \(that is, the settings of the cluster, the namespace, and the role\).
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17456/156231059810618_en-US.png)

    The role permissions that apply to clusters and namespaces are shown in the following table.

    |Role|Cluster management permissions|Application management permissions|
    |----|------------------------------|----------------------------------|
    |Admin|The read and write permissions of clusters. Specifically, the permissions to delete clusters, scale clusters, and add nodes.|The read and write permissions of resources in all namespaces, and those of nodes, volumes, namespaces, and quotas.|
    |Operation|The read and write permissions of clusters. Specifically, the permissions to delete clusters, scale clusters, and add nodes.|The read and write permissions of resources in all namespaces, and the ready permissions of nodes, volumes, namespaces, and quotas.|
    |Developer|The read permissions of clusters.|The read and write permissions of the resources in all namespaces or specified namespaces.|
    |Restricted User|The read permissions of clusters.|The read permissions of resources in all namespaces or specified namespaces.|
    |Custom|The read and write permissions of clusters. Specifically, the permissions to delete clusters, scale clusters, and add nodes.|The permissions of the RAM user depend on the cluster role you select. Confirm the permissions that your selected cluster role has on resources before authorization, to avoid inappropriate permissions granted to the RAM user.|

5.  If **Authorized success** is displayed, this means that you have granted the target RAM user the permissions. If **Add authorization policies** is displayed, follow these steps:
    -   If you are using a RAM user, you need to log out, and then use an account to log on to the console to grant required permissions to the target RAM user.
    -   If you are using a primary account, you can directly perform the next step.
6.  Click **Copy**, and then click **Policy Management**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17456/156231059844121_en-US.png)

7.  Choose **Permissions** \> **Policies**, and then click **Create Policy**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17456/156231059844133_en-US.png)

8.  Enter a **Policy Name**, select the **Script** configuration mode, use the hot key **Ctrl+V** to paste the content that was copied in step 6 in the **Policy Document** area, and then click **OK**. For more information, see [Create custom authorization policies](reseller.en-US/User Guide/Kubernetes cluster/Authorization management/Create custom authorization policies.md#).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17456/156231059844122_en-US.png)

9.  In the left-side navigation pane, choose **Identities** \> **Users**. On the right of the target user, click **Add Permissions**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17456/156231059844124_en-US.png)

10. Select **Custom Policy**, search for or manually look for the customized policy, click the policy name to add the policy to the **Selected** area on the right, and then click **OK**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17456/156231059844125_en-US.png)

    **Note:** After you complete these steps, you can use the target RAM user to log on to the Container Service console and perform the operations allowed by the granted permissions. Typically, if the target RAM user is granted the admin permissions, you can use it to grant permissions to other RAM users.


## Custom permissions {#section_yrt_rxb_pfb .section}

Alibaba Cloud Container Service offers four types of permissions by pre-setting four types of roles: Admin, Operation, Developer, and Restricted User. These types of permissions can meet the needs of most users in the Container Service console. However, if you want to customize the access permissions to clusters, you can also use the custom permissions.

**Note:** Alibaba Cloud Container Service provides several custom permission. Among them, the cluster-admin permission is a super administrator permission with the permissions to access and operate on all resources.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17456/156231059914375_en-US.png)

You can log on to the cluster Master node and run the following command to view the details of the custom permissions.

**Note:** Only some of the cluster roles are displayed.

```
# kubectl get clusterrole
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

To view the permission details of the super administrator cluster-admin, run the following command.

**Note:** After the RAM user is granted the cluster-admin role, the RAM user can be regarded as a super administrator that has the same privileges as the Alibaba Cloud account, and it can perform operations on any resources in the cluster. Execute caution when you grant the cluster-admin role.

```
# kubectl get clusterrole cluster-admin  -o yaml
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

