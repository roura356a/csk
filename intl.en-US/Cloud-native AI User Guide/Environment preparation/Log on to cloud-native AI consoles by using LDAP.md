---
keyword: [LDAP, integrate the cloud-native AI component set with an external LDAP system]
---

# Log on to cloud-native AI consoles by using LDAP

The cloud-native AI component set supports integration with external Lightweight Directory Access Protocol \(LDAP\) systems. This allows you to log on to cloud-native AI consoles by using corporate account credentials instead of using the credentials of Resource Access Management \(RAM\) users. This topic describes how to integrate with an external LDAP system to log on to cloud-native AI consoles.

-   The cloud-native AI component set is deployed. For more information, see [Deploy the cloud-native AI component set](/intl.en-US/Cloud-native AI User Guide/Environment preparation/Deploy the cloud-native AI component set.md).
-   An LDAP system is set up. For more information, see [Set up an LDAP authentication source for ACK](/intl.en-US/Best Practices/Operation and maintenance/Set up an LDAP authentication source for ACK.md).
-   Keycloak is deployed. For more information, see [Deploy a Keycloak application](https://bp.aliyun.com/detail/152?spm=a2cls.b8387508.0.0.4e784a21BBnyE7).

## Terms

-   LDAP: a protocol for managing and accessing account systems. LDAP is mostly implemented in the following two ways:

    -   Active Directory \(AD\): a directory service developed by Microsoft. LDAP 2.0 and 3.0 are supported.
    -   OpenLDAP: an open source implementation of LDAP. LDAP 2.0 and 3.0 are supported.
    For more information, see [LDAP](https://ldap.com/?spm=a2c4g.11186623.2.12.48d333f3CKmwHN).

-   Alibaba Cloud Identity as a Service \(IDaaS\): a centralized identity, permission, and application management service provided by Alibaba Cloud for enterprise users. IDaaS helps users streamline identities for on-premises and cloud-based internal office systems, business systems, and third-party SaaS systems. This way, a user can access all application systems by using only one account. IDaaS supports integration with third-party identity providers \(IdPs\) for data exchange and synchronization.
-   IdP: The identity provider that verifies user identity when a user attempts to log on.
-   RAM: an identity and access control service provided by Alibaba Cloud. RAM manages users, roles, and permissions, and supports single sign-on \(SSO\).
-   SSO: an authentication method that allows a user to use one set of logon credentials to access multiple trusted application systems.
-   OAuth 2.0: a protocol that allows a user to grant an application access to the resources of users. For more information, see [OAuth 2.0](https://oauth.net/2/).
-   OpenID Connect \(OIDC\): an identity layer built on top of the OAuth 2.0 framework.
-   Security Assertion Markup Language \(SAML\): a standard protocol for implementing user identity authentication at the enterprise level. SAML is a solution to data exchange between a service provider \(SP\) and an IdP. SAML 2.0 is an industry standard that is used to implement SSO at the enterprise level.

    -   SP: The service provider that uses the user information provided by the IdP. In some identity systems that are not based on SAML, such as OIDC, an SP is known as the relying party of an IdP.
    -   IdP: The identity provider that verifies user identity when a user attempts to log on.
    SAML supports IdP-initiated SSO and SP-initiated SSO. After a user logs on to the IdP, the user can log on to a random application. The user can also initiate SSO from an application, and then log on to the IdP. After authentication, the user can log back on to the application and initiate SSO from there.

-   System for Cross-domain Identity Management \(SCIM\): a standard for managing user identities in cloud-based applications and services. Alibaba Cloud synchronizes account data from corporate internal systems to RAM based on SCIM and security authorization of OAuth applications. For more information, see [SCIM](http://www.simplecloud.info/?spm=a2c4g.11186623.2.6.aca865fe9KDRKX#Overview).

## How it works

The cloud-native AI O&M console supports RAM user-based SSO. Role-based SSO is not supported. To enable user-based SSO, you must synchronize user information from LDAP to RAM and create RAM users based on the information. The information excludes passwords. Then, you must configure a third-party SSO authentication source in RAM. This way, when you try to log on to RAM, you are redirected to the third-party IdP for authentication.

The procedure involves the following three roles:

-   Installer of the cloud-native AI component set: has management permissions on the cluster. By default, the installer is the first administrator and cannot be deleted.
-   Administrator of the cloud-native AI component set: has access and management permissions on the cloud-native AI O&M console.
-   Researcher of the cloud-native AI component set: has access and management permissions on the cloud-native AI development console.

The procedure consists of the following steps:

1.  The installer of the cloud-native AI component set synchronizes user information from LDAP to RAM. For more information, see [Step 1: Synchronize LDAP accounts to RAM](#section_n08_js4_t05).
2.  Deploy the cloud-native AI component set in your cluster. For more information, see [Deploy the cloud-native AI component set](/intl.en-US/Cloud-native AI User Guide/Environment preparation/Deploy the cloud-native AI component set.md).
3.  The administrator of the cloud-native AI component set creates a new administrator or a researcher. For more information, see [Step 2: Add an administrator in the cloud-native AI O&M console](#section_0ir_2by_flh).
4.  The administrator logs on to the cloud-native AI O&M console. For more information, see [Step 3: Log on to the cloud-native AI O&M console as an LDAP user](#section_4z4_3fm_x2a).

    **Note:** Researchers can perform the same steps to log on to the cloud-native AI development console as the steps that administrators perform to log on to the cloud-native AI O&M console.


The following figure shows the procedure of how an administrator logs on to the cloud-native AI O&M console.

![22](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4503386261/p283915.png)

## Step 1: Synchronize LDAP accounts to RAM

**Note:** You can manually synchronize LDAP accounts to RAM or configure to automate the synchronization process. Select the method based on your scenarios.

-   If you have a small number of corporate accounts, you can manually synchronize the accounts by creating a RAM user for each corporate account.
-   If you have a large number of corporate accounts, you can use IDaaS to automatically synchronize the accounts. IDaaS supports both full and incremental synchronization.

**Method 1: Manually synchronize LDAP accounts to RAM**

1.  Log on to the [RAM console](https://ram.console.aliyun.com/).

2.  In the left-side navigation pane, choose **Identities** \> **Users**.

3.  On the **Users** page, click **Create User**.

4.  On the **Create User** page, set **Logon Name** and **Display Name**. Then, click **OK**.

    **Note:** The logon name must be the same as the username of the account in LDAP.


**Method 2: Use IDaaS to synchronize LDAP accounts to RAM**

1.  Create a RAM user and grant the RAM user the AliyunRAMFullAccess permission. For more information, see [Prepare a RAM user]().

    **Note:**

    -   Skip this step if you already have a RAM user that is granted the AliyunRAMFullAccess permission.
    -   Create an AccessKey pair and save the AccessKey ID and AccessKey secret to your on-premises machine.
2.  Add the Alibaba Cloud RAM - User SSO application in the IDaaS console. For more information, see [Add the application on IDaaS]().

    ![111](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4503386261/p286078.png)

    -   In the preceding figure, you must enter the AccessKey ID and AccessKey secret obtained from Step 1 into the fields highlighted by the red box.
    -   If you want to modify the application information after you save the configurations, go to the **Application List** page, turn off **Application Status**, and then click **Modify Application**.

        ![22](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4503386261/p286102.png)

3.  Synchronize LDAP accounts to IDaaS.

    1.  Configure LDAP.

        1.  Log on to the [IDaaS console](https://yundun.console.aliyun.com/?p=idaas#/overview).
        2.  In the left-side navigation pane of the IDaaS console, click **The list of EIAM instances**.
        3.  On the **Instances** page, find the IDaaS instance that you want to manage and click **Manage** in the **Actions** column.
        4.  In the left-side navigation pane of the instance overview page, choose **Users** \> **Organizations and Groups**.
        5.  In the **View Details** section, click **Configure LDAP**.

            ![12](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4503386261/p284611.png)

        6.  In the **Configure LDAP** panel, click **Create** and set the required parameters. For more information about the parameters, see [Configure LDAP]() and [Step 2: Configure IDaaS](/intl.en-US/Best Practices/Operation and maintenance/Set up an LDAP authentication source for ACK.mdsection_nlw_lmx_xr2).
        7.  After you save the configurations, you can view the new configuration in the **Configure LDAP** panel.

            ![13](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4503386261/p284609.png)

    2.  Import data from AD to an organization and an account. For more information, see [Import data from AD to an organization and an account]().

        Verify the result.

        On the **OUs and Groups** page, click **dev** in the OUs section. You can view the accounts that are synchronized from LDAP on the **Account** tab in the **View Details** section.

        **Note:** If you find accounts that already exist in IDaaS when you synchronize **accounts** from LDAP to IDaaS, you can click **Remove** to remove the accounts.

        ![3](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4503386261/p286126.png)

4.  Synchronize accounts from IDaaS to RAM.

    **Note:** After you complete the preceding three steps, user information is synchronized from LDAP to the **Alibaba Cloud RAM - User SSO** application. Now, you need only to synchronize the user information from the IDaaS application to RAM based on SCIM.

    1.  Configure SCIM. For more information, see [t1857331.md\#section-oa8-sfw-1ce]().

        ![11](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5503386261/p286146.png)

        The following list describes some of the parameters:

        -   SCIM Service URL. You can obtain this parameter value by appending the user import API URL to the domain name of the IDaaS instance. For example, if the domain name of the IDaaS instance is `pvfjoa****.login.aliyunidaas.com` and the user import API URL is `/api/application/plugin_aliyun/scim/account/fdeba622128415bb6da37e0915341d46AEklnKK****`, the SCIM service URL is `[pvfjoa\*\*\*\*.login.aliyunidaas.com/api/application/plugin\_aliyun/scim/account/fdeba622128415bb6da37e0915341d46AEklnKK\*\*\*\*](https://pvfjoapcxl.login.aliyunidaas.com/api/application/plugin_aliyun/scim/account/fdeba622128415bb6da37e0915341d46AEklnKKk9v0)`.
            -   To obtain the domain name of your IDaaS instance, log on to the [IDaaS](https://yundun.console.aliyun.com/?p=idaas#/overview) console.

                ![55](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5503386261/p286153.png)

            -   To obtain the user import API URL, perform the following steps:
                1.  In the left-side navigation pane of the instance overview page, choose **Applications** \> **Application List**.
                2.  Find the **Alibaba Cloud RAM - User SSO** application and click **Details** in the Actions column.
                3.  In the **Application Information** section, click **View Details**. In the **Application Details \(Alibaba Cloud RAM - User SSO\)** panel, find **Account Provisioning URL**. This URL is the user import API URL.
        -   The **client\_id** and **client\_secret** parameters that are used for API authentication during account synchronization.

            In the **API** section, turn on **API**. Then, separately click **API Key** and **API Secret** to save the API key and API secret to your on-premises machine.

            ![66](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5503386261/p286159.png)

    2.  Grant permissions to the application.

        1.  In the left-side navigation pane of the instance overview page, choose **Authorization** \> **Application Authorization**.
        2.  On the **Application Authorization** page, click the **Organization** tab, select the Alibaba Cloud RAM - User SSO application and the dev organization, and then click **Save**.

            ![14](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5503386261/p284661.png)

        3.  In the **System Prompt** message that appears, click **OK**.
    3.  Synchronize user information from IDaaS to RAM.

        1.  In the left-side navigation pane of the instance overview page, choose **Users** \> **Organizations and Groups**.
        2.  On the **OUs and Groups** page, click **dev** in the OUs section. You can view the accounts on the **Account** tab in the **View Details** section.
        3.  Select an account that is synchronized from LDAP to IDaaS and click **Provision Account** in the **Actions** column.
        4.  In the **Provision Account** panel, click **Provision**.

            **Note:** You can also automate the synchronization process. This way, accounts that are imported from LDAP to IDaaS are automatically synchronized to RAM. For more information, see [Configure automatic account synchronization](https://help.aliyun.com/document_detail/162215.html?spm=a2c4g.11186623.6.579.7f7b36cdjMMABk).

    Verify the result.

    1.  Log on to the [RAM console](https://ram.console.aliyun.com/).
    2.  In the left-side navigation pane, choose **Identities** \> **Users**.
    3.  On the **Users** page, if you can find the account that is imported from LDAP to IDaaS, it indicates that the account is successfully synchronized from IDaaS to RAM. Otherwise, it indicates that the synchronization failed.

## Step 2: Add an administrator in the cloud-native AI O&M console

After LDAP accounts are synchronized to RAM, you can find the accounts in the cloud-native AI O&M console.

1.  Log on to the cloud-native AI O&M console. For more information, see [Access AI Dashboard](/intl.en-US/Cloud-native AI User Guide/AI console usage guide/O&M/Access AI Dashboard.md).

2.  In the left-side navigation pane of the cloud-native AI O&M console, choose **User** \> **User List**. On the Add User page, select the user that you want to add as an administrator and grant the user administrative permissions.

3.  On the **User List** page, click **Add**.

4.  In the **Add User** dialog box, set the parameters.

    -   Select the user that you want to add as an administrator from the **User Name** drop-down list.
    -   Select **admin** from the **User Type** drop-down list.
5.  Click **Save**.

    **Note:** After the user is added, you can log on as this user to manage the cloud-native AI O&M console as an administrator. For more information, see [Manage users](/intl.en-US/Cloud-native AI User Guide/AI console usage guide/O&M/Manage users.md).


## Step 3: Log on to the cloud-native AI O&M console as an LDAP user

**Note:** You have synchronized an LDAP account to RAM and created a user in the cloud-native AI O&M console. Now, you need only to configure an SSO authentication source in RAM. This way, you can log on to the cloud-native AI O&M console as an LDAP user.

-   Configure a third-party LDAP authentication source in IDaaS
-   Configure SAML-based IdP-initiated SSO in RAM
-   Configure SSO by using OpenLDAP in RAM

**Method 1: Configure a third-party LDAP authentication source in IDaaS to authenticate LDAP users**

1.  Disable SSO in RAM and enable SSO with IDaaS. For more information, see [RAM User Single Sign-on to Alibaba Cloud Console]() and [Steps 1 to 4 in Log on to the RAM console from IDaaS in a single sign-on manner]().

    **Note:** You can go to the **Application Details \(Alibaba Cloud RAM - User SSO\)** panel and click the **IDaaS IdentityId** parameter to download the SAML metadata file named metadata.xml.

2.  Configure a third-party LDAP authentication source in IDaaS. For more information, see [Steps 1 to 3 in LDAP as Authentication Source]().

    **Note:** On the **Authentication Sources** page, make sure that the status of **LDAP** is **Enabled**.

    The following list describes the parameters in the **Authentication Source Details \(LDAP\)** panel:

    -   **LDAP URL**: Set the value of this parameter to a string that consists of **Server Address** and **Port Number** in the **Configure LDAP** panel in the following format: `ldap://<Server Address>:<Port Number>/`.
    -   **LDAP Base**: Set the value of this parameter to the value of **Base DN** in the **Configure LDAP** panel.
    -   **LDAP Account**: Set the value of this parameter to the value of **Administrator DN** in the **Configure LDAP** panel.
    -   **Filter Condition**: Set the value of this parameter to `cn=$username$`.
    -   **LDAP Encryption Algorithm**: Select MD5.
    -   **Display**: Select this check box.
    Verify the result.

    1.  Obtain and visit the link of the cloud-native AI O&M console. For more information, see [Access AI Dashboard](/intl.en-US/Cloud-native AI User Guide/AI console usage guide/O&M/Access AI Dashboard.md).
    2.  Enter the logon name of the RAM user and click **Next**.
    3.  Click the LDAP icon in the following figure.

        ![8](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5503386261/p286512.png)

    4.  Enter the LDAP account name, password, and verification code. Then, click **Submit**.

        **Note:** If the logon fails, see [FAQ](#section_7ex_2w6_l0y) to troubleshoot the failure.


**Method 2: Configure SAML-based IdP-initiated SSO in RAM to authenticate LDAP users**

If Alibaba Cloud and the identity management system of an enterprise work together to implement user-based SSO, Alibaba Cloud is the SP and the enterprise is the IdP. User-based SSO allows an employee of the enterprise to access Alibaba Cloud resources as a RAM user.

After an administrator configures user-based SSO, Employee Alice can log on to the Alibaba Cloud Management Console. The following figure shows the procedure. For more information, see [Process](/intl.en-US/SSO Management/User-based SSO/Overview of user-based SSO.md).

![28](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5503386261/p284338.png)

1.  Configure the SAML metadata to build trust between Alibaba Cloud and the enterprise, and implement IdP-initiated SSO to Alibaba Cloud. For more information, see [Configure the SAML settings of Alibaba Cloud for user-based SSO](/intl.en-US/SSO Management/User-based SSO/Configure the SAML settings of Alibaba Cloud for user-based SSO.md).

2.  Configure Alibaba Cloud as a trusted SP in the SAML configuration of the IdP used by the enterprise. For more information, see [Configure Alibaba Cloud as a trusted SP for user-based SSO](/intl.en-US/SSO Management/User-based SSO/Configure Alibaba Cloud as a trusted SP for user-based SSO.md).


**Note:**

-   If you use AD FS as your IdP, see [Implement user-based SSO from AD FS](/intl.en-US/SSO Management/User-based SSO/Implement user-based SSO from AD FS.md) for an example on how to implement user-based SSO by using AD FS.
-   If you use Okta as your IdP, see [Implement user-based SSO from Okta](/intl.en-US/SSO Management/User-based SSO/Implement user-based SSO from Okta.md) for an example on how to implement user-based SSO by using Okta.
-   If you use Azure AD as your IdP, see [Implement user-based SSO by using Azure AD](/intl.en-US/SSO Management/User-based SSO/Implement user-based SSO by using Azure AD.md) for an example on how to implement user-based SSO by using Azure AD.

## FAQ

-   Issue

    The following error occurs when I configure Keycloak to connect to OpenLDAP and synchronize user information to Keycloak:

    ```
    keycloak sync faild Failed during import user from LDAP: org.keycloak.models.ModelException: User returned from LDAP has null username! Check configuration of your LDAP mappings. 
    ```

    Solution

    Check the error log of Keycloak and see whether the **Users DN** field is missing or contains space characters.

-   Issue

    After the authentication is complete, the page is not redirected and the following error occurs:

    ```
    unknown sp_code
    ```

    Solution

    Log on to the cloud-native AI O&M console again.

-   Issue

    The following error occurs when an LDAP user that is synchronized from OpenLDAP to RAM attempts to log on to the cloud-native AI O&M console:

    ```
    The user does not exist{&quot;UserPrincipalName&quot;:&quot;newldapuser@198370611786****.onaliyun.com&quot;}
    ```

    Solution

    Check the following items:

    -   Check whether the user exists in the RAM console.
    -   Check whether the **Name ID Format** is set to `email` in Keycloak.
    -   Check whether **Auxiliary Domain** is enabled in RAM and set to the same value as the **Auxiliary Domain** in Keycloak.

