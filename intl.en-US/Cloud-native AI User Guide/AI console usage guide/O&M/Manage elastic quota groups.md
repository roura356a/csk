---
keyword: [elastic quota groups, AI Dashboard]
---

# Manage elastic quota groups

Elastic quotas help you improve resource utilization by implementing resource sharing. This topic describes how to manage elastic quota groups in AI Dashboard.

-   The cloud-native AI component set is installed. For more information, see [Deploy the cloud-native AI component set](/intl.en-US/Cloud-native AI User Guide/Environment preparation/Deploy the cloud-native AI component set.md).
-   The system administrator permissions are acquired. For more information, see [Access AI Dashboard](/intl.en-US/Cloud-native AI User Guide/AI console usage guide/O&M/Access AI Dashboard.md).
-   The cluster must run Kubernetes 1.20 or later.

## Create a quota group

1.  Log on to AI Dashboard as a Resource Access Management \(RAM\) user that has acquired the administrator permissions.

2.  In the left-side navigation pane of AI Dashboard, choose **Resource Quota** \> **Resource Quota List**.

3.  On the **Resource Quota List** page, select a parent node \(a node that does not have namespaces\) and click **Add** to add child nodes.

    **Note:** You can add child nodes only for non-leaf nodes or leaf nodes that do not have namespaces.

    ![Add a quota group](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1122837261/p277258.png)

    1.  Enter a name for the quota group. The name must be unique under its parent node.

    2.  Specify one or more namespaces that are managed by the quota group. If you leave this parameter empty, you can continue to add child nodes.

    3.  Specify the resource types and quotas that you want to manage. The following table describes the parameters.

        |Parameter|Description|Required|
        |---------|-----------|--------|
        |Resource Type|The unique identifier of the resource type.|Yes.|
        |Min|The minimum amount of resources available.|No. If you leave this parameter empty, the minimum amount of resources available is unlimited.|
        |Max|The maximum amount of resources available.|No. If you leave this parameter empty, the maximum amount of resources available is unlimited.|

    4.  Click **Save**.

    After you add the quota group, you can view the newly created quota group on the **Resource Quota List** page.


## Update a quota group

1.  Log on to AI Dashboard as a RAM user that has acquired the administrator permissions.

2.  In the left-side navigation pane of AI Dashboard, choose **Resource Quota** \> **Resource Quota List**.

3.  Select the quota group that you want to update and click **Edit**.

4.  Update the resource quota settings.

5.  Click **Save**.

    ![Update a quota group](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1122837261/p277262.png)


## Delete a quota group

1.  Log on to AI Dashboard as a RAM user that has acquired the administrator permissions.

2.  In the left-side navigation pane of AI Dashboard, choose **Resource Quota** \> **Resource Quota List**.

3.  Select the quota group that you want to delete and click **Delete**.

    **Note:** You can delete only leaf nodes that do not have namespaces. You cannot delete root nodes.

    ![Delete a quota group](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1122837261/p277265.png)


## Search for a quota group

1.  Log on to AI Dashboard as a RAM user that has acquired the administrator permissions.

2.  In the left-side navigation pane of AI Dashboard, choose **Resource Quota** \> **Resource Quota List**.

3.  Enter the name of the quota group that you want to search in the **Name** search box.

4.  Click **Search**.

    **Note:** You can enter the keyword of a quota group name to perform a fuzzy match. All of the nodes and their parent nodes that match the keyword are displayed.

    ![Search for a quota group](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1122837261/p277267.png)


