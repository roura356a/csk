# Use sub-accounts {#concept_c5k_gr2_xdb .concept}

Grant the sub-accounts the corresponding permissions before using the sub-accounts to log on to the Container Service console and perform the operations.

## Step 1 Create sub-accounts and enable console logon {#section_wf1_3r2_xdb .section}

1.  Log on to the [RAM console](https://partners-intl.console.aliyun.com/#/ros).
2.  Click **Users** in the left-side navigation pane. Click **Create User** in the upper-right corner.
3.  Enter the username of the sub-account and then click **OK**.
4.  On the User Management page, click **Manage** at the right of the created sub-account.
5.  Click **Enable Console Logon** in the **Web Console Logon Management** section.
6.  Enter the logon password in the appeared dialog box and click **OK**.

## Step 2 Grant sub-accounts permissions to access Container Service {#section_hcw_3r2_xdb .section}

1.  On the User Management page, click **Authorize** at the right of the created sub-account.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16636/153958258010477_en-US.png)

2.  Select the authorization policy and click 1 to add the policy to the Selected Authorization Policy Name.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16636/153958258010478_en-US.png)

    You can use the following system default authorization policies:

    -   AliyunCSFullAccess: Provides full access to Container Service.
    -   AliyunCSReadOnlyAccess: Provides read-only access to Container Service.
    You can also create custom authorization policies as per your needs and grant the policies to the sub-accounts. For more information, see [Create custom authorization policies](reseller.en-US/User Guide/Kubernetes cluster/授权管理/Create custom authorization policies.md#).


## Step 3 Log on to Container Service console with sub-accounts {#section_vjg_hr2_xdb .section}

Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs) with a sub-account.

If you have granted the AliyunCSDefaultRole and AliyunCSClusterRole roles to the main account, you can use the sub-account directly to log on to the Container Service console and perform the operations.

If you have not granted the AliyunCSDefaultRole or AliyunCSClusterRole roles to the main account before, click **Confirm Authorization Policy** in the appeared Cloud Resource Access Authorization page.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16636/153958258010479_en-US.png)

Then, refresh the Container Service console to perform the operations.

