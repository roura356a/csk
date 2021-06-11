---
keyword: [elastic quota groups, AI Dashboard]
---

# Manage elastic quota groups

Elastic quotas help you improve resource utilization by implementing resource sharing on the basis of resource allocation. This topic describes how to manage elastic quota groups in AI Dashboard.

-   The cloud-native AI component set is installed. For more information, see [Deploy cloud-native AI component set](/intl.en-US/Cloud-native AI user guide/Environment preparation/Deploy cloud-native AI component set.md).
-   The system administration permissions are acquired. For more information about the authorization, see [Access AI Dashboard](/intl.en-US/Cloud-native AI user guide/AI console usage guide/O&M/Access AI Dashboard.md).

## Create a quota group

1.  Log on to AI Dashboard as a Resource Access Management \(RAM\) user that has acquired the administration permissions.

2.  In the left-side navigation pane of AI Dashboard, choose **Resource Quota** \> **Resource Quota List**.

3.  On the **Resource Quota List** page, click **Add**.

    1.  Enter a name for the quota group. Required.

    2.  Specify the namespace that you want the quota group to manage. Required.

    3.  Specify the resource that you want the quota group to manage and the resource quota. The fields are described in the following table:

        |Field|Description|Required|
        |-----|-----------|--------|
        |Authorization type|The unique identifier of the resource.|Yes.|
        |Min|The minimum resource usage.|No. An empty value specifies that no limit is set on the minimum resource usage.|
        |Max|The maximum resource usage.|No. An empty value specifies that no limit is set on the maximum resource usage.|

    4.  Click **Save**.


## Update a quota group

1.  Log on to AI Dashboard as a RAM user that has acquired the administration permissions.

2.  In the left-side navigation pane of AI Dashboard, choose **Resource Quota** \> **Resource Quota List**.

3.  Select the quota group that you want to update and click **Edit**.

4.  Update the resource quota settings.

5.  Click **Save**.


## Delete a quota group

1.  Log on to AI Dashboard as a RAM user that has acquired the administration permissions.

2.  In the left-side navigation pane of AI Dashboard, choose **Resource Quota** \> **Resource Quota List**.

3.  Select the quota group that you want to delete and click **Delete**.


## Search for a quota group

1.  Log on to AI Dashboard as a RAM user that has acquired the administration permissions.

2.  In the left-side navigation pane of AI Dashboard, choose **Resource Quota** \> **Resource Quota List**.

3.  Enter the name of the quota group that you want to search for in the **Name** search box.

4.  Click **Search**.

    **Note:** Only exact matches can be performed.


