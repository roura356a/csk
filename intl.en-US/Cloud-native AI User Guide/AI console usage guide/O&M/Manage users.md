---
keyword: [AI Dashboard, manage users]
---

# Manage users

In AI Dashboard, you can create kubeconfig files that store configurations for new users to access specific clusters. You can also query, delete, and modify users in AI Dashboard. This topic describes how to manage users in AI Dashboard.

-   The cloud-native AI component set is installed. For more information, see [Deploy the cloud-native AI component set](/intl.en-US/Cloud-native AI User Guide/Environment preparation/Deploy the cloud-native AI component set.md).
-   The credentials of the Resource Access Management \(RAM\) user that is specified as the administrator of AI Dashboard are obtained.
-   A quota group is created. For more information, see [Create a quota group](/intl.en-US/Cloud-native AI User Guide/AI console usage guide/O&M/Manage elastic quota groups.md).

## Create a kubeconfig file for a new user

**Note:** For more information about how to manage users by using a CLI, see [Best practice for using Arena in a multi-tenant scenario]().

1.  After **AI Dashboard** and **AI Developer Console** are installed in the cluster, you can find two hyperlinks on the **Cloud-native AI Component Set** page, as shown in the following figure. Then, click **O&M Console**.

    ![35](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3523594261/p276642.png)

    For more information, see [Deploy the cloud-native AI component set](/intl.en-US/Cloud-native AI User Guide/Environment preparation/Deploy the cloud-native AI component set.md).

2.  In the left-side navigation pane of AI Dashboard, choose **User** \> **User List**.

3.  On the **User List** page, click **Add**.

4.  In the **Add User** dialog box, set the parameters:

    1.  Specify **User Name**, **User Type**, and **Cluster Roles**. You can select one or more user types and cluster roles.

    2.  Set the **Roles** parameter. Click the ![40](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8632837261/p278189.png) icon and specify **Namespace** and **Role**.

    3.  Click **Save**.

5.  After the new user is created, you can view and download the kubeconfig file on the User List page.

6.  Find the created new user and click **Download** in the **KubeConfig** column.

    You can send a copy of the kubeconfig file to the user. Then, the user can access the relevant cluster with the kubeconfig file.


## Manage users in AI Dashboard

**Search for a user**

1.  Log on to AI Dashboard as an administrator.

2.  In the left-side navigation pane of AI Dashboard, choose **User** \> **User List**.

3.  On the **User List** page, enter the **name of the user** that you want to search for and click **Search**.

4.  Click **Search**.


**Delete a user**

1.  Log on to AI Dashboard as an administrator.

2.  In the left-side navigation pane of AI Dashboard, choose **User** \> **User List**.

3.  Find the user that you want to delete and click **Delete** in the **Operator** column.


