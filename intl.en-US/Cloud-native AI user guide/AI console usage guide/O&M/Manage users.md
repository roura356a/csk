---
keyword: [AI Dashboard, manage users]
---

# Manage users

In AI Dashboard, you can create kubeconfig files that new users can use to configure access to clusters. You can also query, delete, and modify users. This topic describes how to manage users in AI Dashboard.

-   The cloud-native AI component set is installed. For more information, see [Deploy cloud-native AI component set](/intl.en-US/Cloud-native AI user guide/Environment preparation/Deploy cloud-native AI component set.md).
-   The credentials of the Resource Access Management \(RAM\) user that is configured as the administrator of AI Dashboard are obtained.
-   A quota group is created. For more information, see [Create a quota group](/intl.en-US/Cloud-native AI user guide/AI console usage guide/O&M/Manage elastic quota groups.md).

## Create a kubeconfig file for a new user

**Note:** For more information about how to manage users by using a command line tool, see [Best practice for using Arena in a multi-tenant scenario](/intl.en-US/Solutions/Deep learning solution/Best practice for using Arena in a multi-tenant scenario.md).

1.  Log on to AI Dashboard by using the credentials of the RAM user that is configured as the administrator.

2.  In the left-side navigation pane of AI Dashboard, choose **User** \> **User List**.

3.  On the **User List** page, click **Add**.

4.  In the **Add User** dialog box, set **User Name** and **Namespace**, and then click **Save**.

5.  After the new user is created, you can view and download the kubeconfig file on the User List page.

6.  Find the created user and click **Download** in the **KubeConfig** column.

    You can use the kubeconfig file to configure cluster access for the user.


## Manage users in AI Dashboard

**Search for a user**

1.  Log on to AI Dashboard by using the credentials of the administrator.

2.  In the left-side navigation pane of AI Dashboard, choose **User** \> **User List**.

3.  On the **User List** page, enter the **name of the user** that you want to search for and click **Search**.

4.  Click **Search**.


**Delete a user**

1.  Log on to AI Dashboard by using the credentials of the administrator.

2.  In the left-side navigation pane of AI Dashboard, choose **User** \> **User List**.

3.  Find the user that you want to delete and click **Delete** in the **Operator** column.


