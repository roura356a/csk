---
keyword: [ACK, LDAP, IDaaS]
---

# Set up an LDAP authentication source for ACK

An increasing number of enterprise users of Alibaba Cloud Container Service for Kubernetes \(ACK\) require to use their own account systems in the cloud. However, the migration of these account systems to the cloud may not be a smooth process. The challenge is how to smoothly migrate their accounts to the cloud without registering a large number of new accounts and managing different username/password pairs. This topic describes how to set up a Lightweight Directory Access Protocol \(LDAP\) authentication source to map external users to Alibaba Cloud Resource Access Management \(RAM\) users.

The following tools and Alibaba Cloud services are used in this topic:

-   [ACK](/intl.en-US/Product Introduction/What is Container Service for Kubernetes?.md).
-   [Resource Access Management \(RAM\)](/intl.en-US/Product Introduction/What is RAM?.md).
-   [Identity as a service \(IDaaS\)](). Alibaba Cloud provides this service to integrate external account systems with the Alibaba Cloud account system.
-   [LDAP](https://ldap.com/). LDAP stores the account information and for single sign-on \(SSO\). OpenLDAP is the open source implementation of LDAP.

## How it works

The LDAP authentication source is integrated with the Alibaba Cloud account system through the following steps:

1.  Add an LDAP authentication source in the IDaaS console, and synchronize LDAP accounts to IDaaS. The passwords are not synchronized. If you want LDAP users to log on to ACK with their LDAP accounts, you must set a password for each LDAP account in IDaaS. To avoid trouble of setting a password for each account, you can also configure single sign-on.
2.  Add an application in the IDaaS console.
    -   The application must be linked to a RAM user that has the AliyunRAMFullAccess permission so that the application has the full permissions to manage RAM. Therefore, you must specify the AccessKey ID and AccessKey secret of the RAM user in the application configuration.
    -   The application must also be linked to another RAM role or RAM user that is used to grant the LDAP accounts the permissions to manage ACK .
    -   You must import the LDAP accounts that have been synchronized to IDaaS to the application, and authorize these accounts to use the application. This enables the LDAP accounts to derive the required permissions from the RAM role or RAM user that is linked to the application.
3.  Set up an LDAP authentication source for IDaaS SSO.
4.  When LDAP users log on to the Alibaba Cloud Management Console through SSO, they derive the required permissions from the RAM role or RAM user that is linked to the application in IDaaS.
5.  Grant the platform as a service \(PaaS\) permissions to the IDaaS account in the ACK console.

## Step 1: Prepare the environment

Perform the following steps to build a staging environment for LDAP:

1.  Run the following command to install OpenLDAP.

    You can use OpenLDAP to provision an LDAP data store and configure php-LDAP-admin to manage LDAP.

    ```
    git clone https://github.com/lilongthinker/demo-base-code.git
    
    # Output:
    Cloning to 'demo-base-code'...
    remote: Enumerating objects: 12, done.
    remote: Counting objects: 100% (12/12), done.
    remote: Compressing objects: 100% (10/10), done.
    remote: Total 12 (delta 0), reused 9 (delta 0), pack-reused 0
    Unpacking objects: 100% (12/12), done.
    ```

    ```
    tmp cd demo-base-code/01_ldap
    01_ldap git:(master) tree . /
    
    # Output:
    ./
    ├── ingress-phpadmin.yaml
    ├── ldap-deploy.yaml
    ├── ldap-secret.yaml
    ├── ldap-service.yaml
    ├── phpldapadmin-deploy.yaml
    └── phpldapadmin-svc.yaml
    
    0 directories, 6 files
    ################################################ 
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: ldap-ui
      namespace: public-service
    spec:
      rules:
      - host: phpldap.c26a7ab225d1544088735677ed906xxxx.cn-beijing.alicontainer.com # Replace the value with the domain name of your cluster.
        http:
          paths:
          - backend:
              serviceName: phpldapadmin
              servicePort: 8080
    ## Run the vim command to replace the value of the host field with the domain name of your cluster.
    ################################################
    ```

    ```
    01_ldap git:(master) kubectl create ns public-service
    
    # Output:
    namespace/public-service created
    ```

    ```
    01_ldap git:(master) kubectl apply -f . /
    
    # Output:
    ingress.extensions/ldap-ui created
    deployment.extensions/ldap created
    secret/ldap-secret created
    service/ldap-service created
    deployment.extensions/phpldapadmin created
    service/phpldapadmin created
    ```

2.  Initialize LDAP accounts.

    1.  Log on to php-LDAP-admin.

        1.  Run the following command to query the domain name and IP address of the Ingress:

            ```
            01_ldap_with_ui git:(master) ✗ kubectl get ing
            ```

            ```
            NAME      HOSTS                                                                   ADDRESS          PORTS   AGE
            ldap-ui   phpldap.c26a7ab225d1544088735677ed906xxxx.cn-beijing.alicontainer.com   121.xx.xxx.xxx   80      45s
            ```

        2.  Copy the domain name into the address bar of your browser and press Enter to open the console of php-LDAP-admin. Then, use the default distinguished name \(DN\) and password to log on to the console.

            **Note:**

            -   The default DN: cn=admin,dc=example,dc=org.
            -   The default password: admin.
    2.  Create organizations and accounts.

        1.  In the left-side navigation pane, click **Create new entry here**. On the Create Object page, click **Generic: Organisational Unit**, and then click **Create Object**.

            ![unit](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8826369951/p141650.png)

        2.  Enter a name for the organization and click **Create Object**. In this example, the organization name is dev.
        3.  In the left-side navigation pane, click the newly created ou=dev. On the ou=dev page, click **Create a child entry**, and then click **Courier Mail: Account**. Set the parameters and click **Create Object**.

            ![Account](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8826369951/p141660.png)

            **Note:** The default name of the created account may contain extra space characters. We recommend that you delete the space characters.


## Step 2: Configure IDaaS

Perform the following steps to import the organizations and accounts from LDAP to IDaaS.

1.  Log on to the [IDaaS console](https://yundun.console.aliyun.com/?spm=5176.cnidaas.0.0.1f68782bpjiAoX&p=idaas#/instanceList/cn-hangzhou).

2.  Find and click your IDaaS instance.

3.  In the left-side navigation pane, choose **Users** \> **Organizations and Groups**.

4.  In the **View Details** section, click **Configure LDAP**.

5.  In the **Configure LDAP** pane, click **Create**.

6.  Set the parameters that are required to configure LDAP. For more information about the parameters, see [LDAP provision configuration]().

    The following table describes some of the parameters.

    -   Parameters for server connection

        |Parameter|Description|
        |---------|-----------|
        |AD/LDAP Name|Enter a custom name.|
        |Server Address|Enter the IP address of LDAP that is used for external access. Do not specify the domain name of LDAP.|
        |Port Number|Enter the port number of LDAP that is used for external access. The port number is 389.|
        |Base DN|Enter the LDAP directory to be synchronized to IDaaS.|
        |Administrator DN|Enter an LDAP administrator DN.|
        |Password|Enter the LDAP administrator password.|
        |Select Type|Select **Windows AD** or **OpenLDAP**.|
        |From LDAP to IDaaS|Select **Enable**.|
        |Provision from IDaaS to LDAP|You can select **Enable** or **Disable**.|

    -   Parameters for field matching rules

        |Parameter|Description|
        |---------|-----------|
        |Username|A keyword of the username in LDAP. You can enter cn.|
        |External ID|        -   Enter objectGUID if you select the Windows AD type.
        -   Enter uid if you select the OpenLDAP type. |
        |Password Attribute|        -   Enter unicodePwd if you select the Windows AD type.
        -   Enter userPassword if you select the OpenLDAP type. |
        |User Unique Identifier|        -   Enter DistinguishedName if you select the Windows AD type.
        -   Enter EntryDN if you select the OpenLDAP type. |
        |Email|Enter a keyword, for example, mail.|
        |Phone Number|Enter a keyword, for example, telephoneNumber.|
        |Alias|Enter an alias for the synchronized account in IDaaS.|
        |Default Password|The default password that is used to synchronize the account from Windows AD or OpenLDAP at a specified time.|

    **Note:**

    Note the following limits when you set the preceding parameters:

    -   In the **Account** section, set **Administrator DN** and **Password** to the following values:
        -   Administrator DN: cn=admin,dc=example,dc=org
        -   Password: admin
    -   After you have set all of the preceding parameters on the Server Connection and Field Matching Rule tabs, click **Test Connection** on both tabs. After the test succeeds, click **Save** on both tabs.
    -   If you want to synchronize only specified accounts to Alibaba Cloud, specify **Base DN** as the root directory that you want to synchronize. If you want to synchronize multiple departments in different root directories, configure an LDAP data store for each department.
7.  Import organizations and accounts. For more information, see [Import organizations and accounts from Windows AD]().


## Step 3: Configure IDaaS SSO

1.  Create an application.

    1.  Go to the details page of your IDaaS instance. In the left-side navigation pane, choose **Applications** \> **Add Applications**.

    2.  On the **Add Applications** page, find **plugin\_aliyun\_role** in the **Application ID** column and click **Add Application** in the Actions column.

    3.  In the pane that appears, click **Add SigningKey**. Set the parameters and click **Submit**.

    4.  Click **Select** in the Actions column to configure LDAP.

        **Note:** Enter the AccessKey ID and AccessKey secret of the RAM user that has the AliyunRAMFullAccess permission.

    5.  Click **Submit**.

2.  Authorize the application by organization.

    1.  Go the details page of your IDaaS instance. In the left-side navigation, choose **Authorization** \> **Application Authorization**.

    2.  On the **Authorize OUs or Groups by Application** tab, click the created application and organization.

    3.  In the left-side navigation pane of the instance details page, choose **Application** \> **Application List**. Find the created application and click **Details** in the Actions column. In the **Application Information** section, click **View Details**.

    4.  In the **Application Details** pane, click **Export IDaaS SAML Meta Profile**.

    5.  Log on to the [RAM console](https://ram.console.aliyun.com/overview). In the left-side navigation pane, click **SSO**. On the **Role-based SSO** tab, click **Create IdP**.

    6.  In the **Create IdP** pane, enter the IdP name, click **Upload** to upload the IDaaS SAML meta profile, and then click **OK**.

        ![SSO](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8826369951/p141774.png)

    7.  Create a RAM role, specify **IdP** as the trusted entity of the RAM role, and add the AliyunRAMFullAccess permission. For more information, see [Create a RAM role for a trusted IdP](/intl.en-US/RAM Role Management/Create a RAM role/Create a RAM role for a trusted IdP.md).

    8.  In the left-side navigation pane of the [IDaaS console](https://yundun.console.aliyun.com/), choose **Applications** \> **Application List**. Find the application that you have created and click **Details** in the Actions column. In the **Account Information - Account Linking** section, click **View Application Accounts**.

    9.  On the **Application Accounts** page, click **Link Accounts**.

    10. In the **Application Accounts** pane, enter the IDaaS account and application account, and then click **Save**.

        **Note:**

        The IDaaS account is the name of the LDAP username that is imported on the **Account** tab of the **Organizations and Groups** page.

        You can grant the application account the ACK fullAccess permission by linking the account to the corresponding RAM role or RAM user. If you want to link multiple application accounts to the RAM role or RAM user at a time, use a YAML file.


## Step 4: Set up an LDAP authentication source for SSO in the IDaaS console

1.  In the left-side navigation pane of the [IDaaS console](https://yundun.console.aliyun.com/), choose **Authentication** \> **Authentication sources**.

2.  In the upper-right corner of the **Authentication sources** page, click **Add Authentication Source**.

3.  Find **LDAP** and click **Add Authentication Source** in the Actions column.

4.  Set the parameters and click **Submit**.

    **Note:** Select **Display**. If you do not select Display, the authentication source is not displayed in the login dialog box. For more information about the parameters, see [LDAP as Authentication Source]().


## Step 5: Test SSO

Visit the User Login page to test SSO.

1.  In the left-side navigation pane of the [IDaaS console](https://yundun.console.aliyun.com/), click Instances and find your IDaaS instance on the Instances page.

2.  Copy the URL in the User login page address column into the address bar of your browser and press Enter. In the login dialog box that appears, click the LDAP icon.

    For more information about setting up an LDAP authentication source, see [LDAP as Authentication Source]().


