---
keyword: [LDAP, 云原生AI套件对接外部账号体系]
---

# 通过对接外部LDAP登录云原生AI控制台

云原生AI套件通过对接外部LDAP（Lightweight Directory Access Protocol），借助企业内部账号体系登录云原生AI运维控制台，无需通过RAM账号密码登录。本文介绍云如何通过对接外部LDAP登录云原生AI控制台。

-   安装云原生AI套件。具体操作，请参见[安装云原生AI套件](/intl.zh-CN/云原生AI用户指南/环境准备/安装云原生AI套件.md)。
-   拥有LDAP。具体操作，请参见[ACK对接外部LDAP验证源的步骤一](/intl.zh-CN/最佳实践/运维/ACK对接外部LDAP验证源.md)。
-   部署Keycloak。具体操作，请参见[部署KeyCloak应用](https://bp.aliyun.com/detail/152?spm=a2cls.b8387508.0.0.4e784a21BBnyE7)。

## 基础概念

-   LDAP（Lightweight Directory Access Protocol）：账号管理及访问协议，有以下两种主流的实现方式：

    -   AD（Active Directory）：Microsoft出品，支持LDAP 2.0和3.0。
    -   Open LDAP：Open source LDAP，支持LDAP 2.0和LDAP 3.0。
    更多信息，请参见[LDAP](https://ldap.com/?spm=a2c4g.11186623.2.12.48d333f3CKmwHN)。

-   IDaaS（Alibaba Cloud Identity as a Service）：应用身份服务IDaaS是阿里云为企业用户提供的一套集中式身份、权限、应用管理服务，帮助您整合部署在本地或云端的内部办公系统、业务系统及三方SaaS系统的所有身份，实现一个账号打通所有应用服务。提供接入第三方认证源（IdP）以及数据的交换同步。
-   RAM（Resource Access Management）：阿里云资源访问控制，提供用户、角色、权限管理以及SSO能力。
-   SSO（Single-Sign On）：单点登录，也称为身份联合登录。在多个应用系统中，您只需要登录一次，就可以访问所有相互信任的应用系统。
-   OAuth2.0（Authoration Protocol）：授权访问用户资源的协议。更多信息，请参见[OAuth2.0](https://oauth.net/2/)。
-   OIDC（OpenID Connect）：基于OAuth的ID交换协议。
-   SAML（Security assertion markup language）：实现企业级用户身份认证的标准协议，它是SP和IdP之间实现沟通技术的实现方式之一。SAML 2.0已经是目前实现企业级SSO的一种事实标准。

    -   SP（Service Provider）： 即业务提供方，SP会使用IdP提供的用户信息。一些非SAML协议的身份系统（例如：OpenID Connect），也把服务提供商称作IdP的信赖方。
    -   IdP（Identity Provider）：登录时的身份提供方，认证源。
    SAML同时支持由IdP发起和SP发起的登录， 即在登录IdP后，可以跳转到任意一个应用。也可以从一个应用发起登录，跳转到IdP，登录认证后，再跳转回这个应用，继续SSO。

-   SCIM（System for Cross-domain Identity Management）：主要用于多用户的云应用身份管理。阿里云通过SCIM标准协议，结合OAuth应用的安全授权，可以将企业内部系统中的账号数据同步到阿里云访问控制（RAM）中。更多信息，请参见[SCIM](http://www.simplecloud.info/?spm=a2c4g.11186623.2.6.aca865fe9KDRKX#Overview)。

## 实现原理

云原生AI运维控制台对接的是阿里云访问控制（RAM）的用户单点登录（SSO），非角色SSO。而用户SSO需要同步LDAP用户信息（不包含密码）到RAM作为RAM子账号（RAM用户），同步之后配置RAM第三方SSO认证源，即可在登录RAM的时候，跳转至第三方IdP做登录认证。

共涉及三种角色：

-   云原生AI套件安装者：拥有集群操作权限，安装者默认是第一个管理员且不可删除。
-   云原生AI套件管理员（Admin）：拥有AI运维控制台登录、操作权限。
-   云原生AI套件研发人员（Researcher）：拥有AI开发控制台登录、操作权限。

实现原理的整体流程如下：

1.  云原生AI安装者同步LDAP用户信息到RAM，具体操作，请参见[步骤一： 同步LDAP账号到RAM](#section_n08_js4_t05)。
2.  安装云原生AI套件。具体操作，请参见[安装云原生AI套件](/intl.zh-CN/云原生AI用户指南/环境准备/安装云原生AI套件.md)。
3.  云原生AI管理员创建管理员或者研发人员，具体操作，请参见[步骤二：在AI运维控制台新建管理员](#section_0ir_2by_flh)。
4.  管理员登录AI运维控制台，具体操作，请参见[步骤三：LDAP用户登录AI运维控制台](#section_4z4_3fm_x2a)。

    **说明：** 研发人员登录AI开发控制台与管理员登录AI运维控制台流程相同。


下图为实现管理员登录云原生AI运维控制台的具体交互流程：

![22](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5289254261/p283915.png)

## 步骤一： 同步LDAP账号到RAM

**说明：** 同步LDAP账号到RAM的方法有手动同步和自动自动同步两种，您可以根据实际情况选择相应的同步方式：

-   如果企业账号较少，您可以通过手动同步，为每个用户创建RAM子账号。
-   如果企业账号较多，您可以借助IDaaS自动全量或增量同步。

**方法一：手动同步LDAP账号到RAM**

1.  登录 [RAM 控制台](https://ram.console.aliyun.com/)。

2.  在RAM控制台左侧导航栏中，选择**人员管理** \> **用户**。

3.  在**用户**页面，单击**创建用户**。

4.  在**创建用户**页面，输入**登录名称**和**显示名称**，单击**确定**。

    **说明：** 登录名称与LDAP中的用户名需保持一致。


**方法二：通过IDaaS同步LDAP账号到RAM**

1.  创建具有AliyunRAMFullAccess权限的RAM账号（即RAM用户）。具体操作，请参见[RAM账号准备]()。

    **说明：**

    -   如果您已经有AliyunRAMFullAccess权限的RAM账号，该步骤可跳过。
    -   将生成的AccessKey ID和AccessKey Secret安全保存到本地。
2.  IDaaS添加阿里云控制台。具体操作， 请参见[IDaaS添加阿里云控制台]()。

    ![111](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5289254261/p286078.png)

    -   上图红框中的参数配置需要用到步骤一中的AccessKey ID和AccessKey Secret。
    -   如果在配置完成后，您需要修改阿里云控制台应用的信息，在**应用列表**页面，关闭**应用状态**开关，然后单击**修改应用**即可。

        ![22](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5289254261/p286102.png)

3.  同步LDAP账号到IDaaS。

    1.  新建LDAP对接配置。

        1.  登录[云盾IDaaS管理平台](https://yundun.console.aliyun.com/?p=idaas#/overview)。
        2.  在应用身份管理控制台的左侧导航栏中，单击**EIAM实例列表**。
        3.  在**实例列表**中，单击目标IDaaS实例右侧**操作**列下的**管理**。
        4.  在IDaaS实例详情的左侧导航中，选择**账户** \> **机构及组**。
        5.  在**查看详情**的右侧区域，单击**配置LDAP**。

            ![12](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5289254261/p284611.png)

        6.  在**LDAP配置**面板，单击**新建配置**，配置相关参数。具体操作和相关参数说明，请参见[新建LDAP配置]()和[步骤二：配置IDaaS](/intl.zh-CN/最佳实践/运维/ACK对接外部LDAP验证源.mdsection_nlw_lmx_xr2)。
        7.  参数配置完成后，在**LDAP配置**面板您可以看到刚新建的LDAP。

            ![13](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5289254261/p284609.png)

    2.  从AD导入账户及组织机构。具体操作，请参见[从AD导入账户及组织机构]()。

        结果验证

        在**机构及组**页面，单击下图**dev**，您可以在右侧**查看详情**区域的**账户**页签，查看到从LDAP同步到IDaaS的账号。

        **说明：** 从LDAP同步**用户**到IDaaS时，如果有用户已同步，您可以**一键移除**处理。

        ![3](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6289254261/p286126.png)

4.  从IDaaS同步账号到RAM。

    **说明：** 通过上述3个操作步骤，LDAP中的用户信息已经同步到IDaaS的**阿里云RAM-用户SSO**应用中，您还需要通过SCIM协议把IDaaS应用中的用户，同步到RAM即可。

    1.  配置SCIM同步。具体操作，请参见[配置SCIM同步]()。

        ![11](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6289254261/p286146.png)

        相关参数配置获取方法：

        -   SCIM同步地址=IDaaS实例域名+用户获取API。例如，IDaaS实例域名为`pvfjoa****.login.aliyunidaas.com`，用户获取API为`/api/application/plugin_aliyun/scim/account/fdeba622128415bb6da37e0915341d46AEklnKK****`，则SCIM同步地址为`[pvfjoa\*\*\*\*.login.aliyunidaas.com/api/application/plugin\_aliyun/scim/account/fdeba622128415bb6da37e0915341d46AEklnKK\*\*\*\*](https://pvfjoapcxl.login.aliyunidaas.com/api/application/plugin_aliyun/scim/account/fdeba622128415bb6da37e0915341d46AEklnKKk9v0)`。
            -   登录[云盾IDaaS管理平台](https://yundun.console.aliyun.com/?p=idaas#/overview)，获取当前IDaaS实例域名。

                ![55](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6289254261/p286153.png)

            -   用户获取API的获取方法如下：
                1.  在IDaaS实例详情的左侧导航中，选择**应用** \> **应用列表**。
                2.  单击应用名称为**阿里云RAM-用户SSO**右侧操作列下的**详情**。
                3.  在**应用信息**区域，单击**查看详情**。在**应用详情（阿里云RAM-用户SSO）**面板中，**账户同步地址**即为用户获取API。
        -   用户账户同步接口认证的**client\_id**和**client\_secret**。

            在**API**区域，打开**API**开关，分别单击**API Key**和**API Secret**，将API Key和API Secret安全保存到本地。

            ![66](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6289254261/p286159.png)

    2.  对新应用授权。

        1.  在IDaaS实例详情的左侧导航栏中，选择**授权** \> **应用授权** 。
        2.  在**应用授权**页面，单击**组织机构**页签，选中目标应用和目标组织机构，单击**保存**。

            ![14](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6289254261/p284661.png)

        3.  在**系统提示**对话框功能，单击**确定**。
    3.  同步IDaaS用户信息到RAM。

        1.  在IDaaS实例详情的左侧导航中，选择**账户** \> **机构及组**。
        2.  在**机构及组**页面，单击**dev**，您可以在右侧**查看详情**区域的**账户**页签。
        3.  单击步骤3中从LDAP同步到IDaaS的用户右侧**操作**列下的**账户同步**。
        4.  在**账户同步**面板，单击**同步**。

            **说明：** 您也可以配置自动同步，这样每次从LDAP导入用户到IDaaS时，IDaaS会自动同步用户到RAM。具体操作请参见[自动同步账户配置](https://help.aliyun.com/document_detail/162215.html?spm=a2c4g.11186623.6.579.7f7b36cdjMMABk)。

    结果验证

    1.  登录 [RAM 控制台](https://ram.console.aliyun.com/)。
    2.  在RAM控制台左侧导航栏中，选择**人员管理** \> **用户**。
    3.  在**用户**页面，如果能查看到步骤3中从LDAP同步到IDaaS的用户信息，则说明您已成功从IDaaS同步用户信息到RAM。反之，则说明失败。

## 步骤二：在AI运维控制台新建管理员

同步LDAP用户信息到RAM后，您可以在AI运维控制台看到对应的用户信息。

1.  登录AI运维控制台。具体操作，请参见[访问AI运维控制台](/intl.zh-CN/云原生AI用户指南/AI控制台使用指南/运维/访问AI运维控制台.md)。

2.  在AI运维控制台的左侧导航栏，选择**用户** \> **用户列表**。在创建用户页面下拉选择需要作为管理员的用户，并授权为管理员用户。

3.  在**用户列表**页面，单击**添加**。

4.  在**新建用户**对话框中：

    -   从**用户**下拉列表中，选择目标用户作为管理员用户
    -   从**用户类型**下拉列表中，选择**admin**。
5.  单击**保存**。

    **说明：** 添加完成后，该用户即可作为管理员身份操作AI运维控制台。具体操作，请参见[管理用户](/intl.zh-CN/云原生AI用户指南/AI控制台使用指南/运维/管理用户.md)。


## 步骤三：LDAP用户登录AI运维控制台

**说明：** 同步LDAP用户信息到RAM，并在AI运维控制台创建完用户后，您只需要通过配置RAM的用户SSO认证源，就能达到通过LDAP登录到AI运维控制台的目的：

-   通过IDaaS接入第三方LDAP认证源。
-   通过RAM对接支持SAML协议的IdP登录。
-   通过RAM对接OpenLDAP登录。

**方法一：LDAP用户通过IDaaS接入第三方LDAP认证源登录AI运维控制台**

1.  用IDaaS SSO接管RAM SSO。具体操作，请参见[阿里云控制台中配置SSO单点登录]()和[从IDaaS单点登录到阿里云控制台的步骤1~4]()。

    **说明：** SAML元数据配置metadata.xml文件需从**应用详情（阿里云RAM-用户SSO）**面板中的**IDaaS IdentityId**参数位置下载。

2.  通过IDaaS接入第三方LDAP认证源，具体操作，请参见[LDAP认证登录的步骤1~3]()。

    **说明：** 在**认证源**页面，请确保**LDAP**的状态是**打开**。

    **认证源详情（LDAP）**面板中的相关参数说明如下：

    -   **LDAP URL**：需要用到**LDAP配置**面板中的**服务器地址**和**端口号**，具体格式为：`ldap://<服务器地址>:<端口号>/`。
    -   **LDAP Base**：即为**LDAP配置**面板中的**Base DN**参数值。
    -   **LDAP账户**：即为**LDAP配置**面板中的**管理员DN**参数值。
    -   **过滤条件**：`cn=$username$`。
    -   **LDAP加密方式**：MD5。
    -   **显示**：选中。
    结果验证

    1.  获取且访问AI运维控制台链接。相关操作，请参见[访问AI运维控制台](/intl.zh-CN/云原生AI用户指南/AI控制台使用指南/运维/访问AI运维控制台.md)。
    2.  输入RAM用户登录名，单击**下一步**。
    3.  单击下图中LDAP认证源图标。

        ![8](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6289254261/p286512.png)

    4.  输入LDAP账户名、密码和验证码，单击**提交**。

        **说明：** 如果不能成功登录，请参见[常见问题](#section_7ex_2w6_l0y)排查处理。


**方法二：LDAP用户通过RAM对接支持SAML协议的IdP登录AI运维控制台**

阿里云与企业进行用户SSO时，阿里云是服务提供商（SP），而企业自有的身份管理系统则是身份提供商（IdP）。通过用户SSO，企业员工在登录后，将以RAM用户访问阿里云。

当管理员在完成用户SSO的相关配置后，企业员工Alice可以通过如下图所示的方法登录到阿里云。更多信息，请参见[基本流程](/intl.zh-CN/单点登录管理（SSO）/用户SSO/用户SSO概览.md)。

![28](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6289254261/p284338.png)

1.  通过配置相应元数据来建立阿里云对企业IdP的信任，实现企业IdP通过用户SSO登录阿里云。具体操作，请参见[进行用户SSO时阿里云SP的SAML配置](/intl.zh-CN/单点登录管理（SSO）/用户SSO/进行用户SSO时阿里云SP的SAML配置.md)。

2.  在企业身份提供商中配置阿里云为可信SAML服务提供商。具体操作，请参见[进行用户SSO时企业IdP的SAML配置](/intl.zh-CN/单点登录管理（SSO）/用户SSO/进行用户SSO时企业IdP的SAML配置.md)。


**说明：**

-   如果您的IdP是AD FS，则关于如何使用AD FS进行用户SSO的示例，请参见[使用AD FS进行用户SSO的示例](/intl.zh-CN/单点登录管理（SSO）/用户SSO/使用AD FS进行用户SSO的示例.md)。
-   如果您的IdP是Okta，则关于如何使用Okta进行用户SSO的示例，请参见[使用Okta进行用户SSO的示例](/intl.zh-CN/单点登录管理（SSO）/用户SSO/使用Okta进行用户SSO的示例.md)。
-   如果您的IdP是Azure AD，则关于如何使用Azure AD进行用户SSO的示例，请参见[使用Azure AD进行用户SSO的示例](/intl.zh-CN/单点登录管理（SSO）/用户SSO/使用Azure AD进行用户SSO的示例.md)。

## 常见问题

-   问题现象

    当配置Keycloak对接OpenLDAP，并同步用户至Keycloak时，出现以下报错：

    ```
    keycloak sync faild Failed during import user from LDAP: org.keycloak.models.ModelException: User returned from LDAP has null username! Check configuration of your LDAP mappings. 
    ```

    解决方法

    检查Keycloak的报错日志，且排查**Users DN**字段是否缺失或有空格。

-   问题现象

    当认证完成后，不能正常跳转，出现以下报错：

    ```
    unknown sp_code
    ```

    解决方法

    重新登录AI运维控制台。

-   问题现象

    当LDAP用户通过RAM对接OpenLDAP登录AI运维控制台时，出现以下报错：

    ```
    该用户不存在 {&quot;UserPrincipalName&quot;:&quot;newldapuser@198370611786****.onaliyun.com&quot;}
    ```

    解决方法

    确认以下几点：

    -   用户是否在RAM中存在。
    -   Keycloak中**Name ID Format**是否为`email`。
    -   SSO登录设置需要开启**辅助域名**，且**辅助域名**与keycloak中需保持一致。

