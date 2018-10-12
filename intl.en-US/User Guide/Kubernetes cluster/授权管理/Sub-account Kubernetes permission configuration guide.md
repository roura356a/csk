# Sub-account Kubernetes permission configuration guide {#concept_qlf_lv4_f2b .concept}

This document helps you understand how to configure the Kubernetes Resource Access Management \(RAM\) cluster permissions for sub-accounts and the corresponding Kubernetes RBAC application permissions within the cluster through the Container Service console .

## Prerequisite {#section_ktd_rvn_42b .section}

-   The sub-account authorization page is visible only to the main account. You have an Alibaba Cloud main account and one or several sub-accounts.
-   Due to the security restrictions of Alibaba Cloud RAM, when you modify the sub-account RAM authorization after you pass the authorization of the Container Service console, manually perform authorization on the RAM console for the target sub-account according to the reference policy content and operation instructions on the page.

## Procedure {#section_ot2_4v4_f2b .section}

**Note:** Use your main account to log on to the Container Service console because the sub-account authorization page is visible only to the main account.

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).
2.  Under the Kubernetes menu, click **Clusters** \> **Authorization** in the left-side navigation pane.
3.  In the sub-account list, select the sub-account that requires authorization, and click **Authorize**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17456/153933442910587_en-US.png)

4.  On the Resource Authorization page, you can add permission configurations of the cluster or namespace level by clicking the plus sign in the upper left corner of the table, and then select an role. You can also click the minus sign at the beginning of the configuration line to remove the target role.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17456/153933442910618_en-US.png)

    For information about definitions of roles in clusters and namespaces, see the permission description below:

    | |Cluster management permissions|Application management permissions|
    |--|------------------------------|----------------------------------|
    |Admin|Read and write permissions of clusters. Delete clusters, scale clusters, and add nodes.|Read and write permissions of resources in all namespaces. Read and write permissions of nodes, volumes, namespaces, and quotas.|
    |Operation|Read and write permissions of clusters. Delete clusters, scale clusters, and add nodes.|Read and write permissions of resources in all namespaces, Read only permissions of nodes, volumes, namespaces, and quotas.|
    |Developer|Read only permissions of clusters.|Read and write permissions of resources in all namespaces or specified namespace.|
    |Restricted Users|Read only permissions of clusters.|Read only permissions of resources in all namespaces or specified namespace.|
    |Custom|Read and write permissions of clusters. Delete clusters, scale clusters, and add nodes.|The permissions are determined by the ClusterRole you select. Please confirm permissions that your selected ClusterRole have on resources before granting the permissions so as to avoid the condition that the sub-account obtains unexpected permissions.|

5.  After the configuration is completed, if you change the target sub-account RAM cluster permission, the Kubernetes cluster RAM permission reference configuration corresponding to the configuration item is displayed on the Update Authorization Policy page. You can complete the sub-account authorization update in the RAM console according to the instructions on the page.

## Additional instructions {#section_q1y_pv4_f2b .section}

To avoid affecting the normal use of currently accessible Kubernetes clusters by the sub-accounts, the Container Service console is temporarily compatible with the old cluster access permission control. In a period of time, the original sub-account can access the Kubernetes cluster without RBAC application permission check. If you are a sub-account user, please contact the main account in time for authorization according to the cluster type and compatibility method below.

For the existing cluster created by the current sub-account, you can complete the automatic upgrade of the cluster application permissions by clicking **Upgrade current cluster authorization information** on the cluster details page.

After the end of the notice period, if a sub-account obtains no authentication from the main account or gets no permission management update, the sub-account is banned from accessing the application console corresponding to the cluster.

|Compatible cluster type|Compatibility method|
|-----------------------|--------------------|
|Clusters created by an existing sub-account|Prompt permission management upgrade notice and provide one-click upgrade link. The sub-account can complete application authorization by clicking the upgrade link.|
|Clusters with access authorized by an existing RAM|Prompt the permission management upgrade notice: Please contact the main account to complete the application authorization.|
|Clusters with access authorized by a newly created RAM|Prompt the permission management upgrade notice: Please contact the main account to complete the application authorization.|

