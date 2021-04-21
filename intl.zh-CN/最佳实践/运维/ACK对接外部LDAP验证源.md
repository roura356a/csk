---
keyword: [容器服务Kubernetes, LDAP, IDAAS]
---

# ACK对接外部LDAP验证源

随着企业客户上云进程的推进，有些客户有自己的账户体系，而上云后这些体系如何平滑迁移到云上？如何避免大量账号的注册和多份用户名密码的管理？为解决这些问题，本文介绍如何在阿里云上完成对自建LDAP（ 轻量目录访问协议）的对接过程。

本文涉及到的产品如下：

-   [容器服务Kubernetes版（Container Service for Kubernetes）](/intl.zh-CN/产品简介/什么是容器服务Kubernetes版.md)。
-   [访问控制RAM（Resource Access Management）](/intl.zh-CN/产品简介/什么是访问控制.md)。
-   [LDAP轻型目录访问协议](https://ldap.com/)：用来保存帐户信息，实现单点登录。OpenLDAP是LDAP的开源实现。

## 对接原理

阿里云上完成对自建LDAP的对接的原理如下：

1.  IDaaS添加LDAP对接配置，同步LDAP账号进入IDaaS，密码不同步。此时使用LDAP用户名登录需要在IDaaS中重新设置密码（如果您不想使用多套密码，可以使用以下SSO方式）。
2.  IDaaS中添加应用：
    -   应用需要通过访问密钥（AccessKey）对接RAM中有AliyunRAMFullAccess权限的子账号，用于应用对RAM权限的操控。
    -   应用与RAM中另外的一个Role或RAM账号对接，用于赋权给LDAP中用于登录的账号。
    -   应用需要导入IDaaS中已经导入阿里云系统的LDAP账号，允许这些账号使用本应用，权限为应用中对接的Role或RAM账号权限。
3.  配置IDaaS单点登录对接的LDAP服务。
4.  LDAP用户通过SSO登录阿里云平台，获取IDaaS中某应用里对接的Role或RAM账号权限。
5.  主账号在容器平台进行PaaS层赋权。

## 步骤一：环境准备

模拟LDAP环境操作步骤如下。

1.  执行以下命令搭建OpenLDAP。

    搭建OpenLDAP来模拟LDAP数据源和LDAP的管理工具php-LDAP-admin。

    1.  执行以下命令复制库代码。

        ```
        git clone https://github.com/lilongthinker/demo-base-code.git
        ```

        预期输出：

        ```
        正克隆到 'demo-base-code'...
        remote: Enumerating objects: 12, done.
        remote: Counting objects: 100% (12/12), done.
        remote: Compressing objects: 100% (10/10), done.
        remote: Total 12 (delta 0), reused 9 (delta 0), pack-reused 0
        展开对象中: 100% (12/12), 完成.
        ```

    2.  执行以下命令修改域名。

        ```
        tmp cd demo-base-code/01_ldap
        01_ldap git:(master) tree ./
        ```

        预期输出：

        ```
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
          - host: phpldap.c26a7ab225d1544088735677ed906xxxx.cn-beijing.alicontainer.com #需要按照您的集群信息修改域名。
            http:
              paths:
              - backend:
                  serviceName: phpldapadmin
                  servicePort: 8080
        ##需要使用vim命令修改上述host的内容为自己集群的域名。
        ################################################
        ```

    3.  执行以下命令创建服务。

        ```
        01_ldap git:(master) kubectl create ns public-service
        ```

        预期输出：

        ```
        namespace/public-service created
        ```

    4.  执行以下命令部署应用。

        ```
        01_ldap git:(master) kubectl apply -f ./
        ```

        预期输出：

        ```
        ingress.extensions/ldap-ui created
        deployment.extensions/ldap created
        secret/ldap-secret created
        service/ldap-service created
        deployment.extensions/phpldapadmin created
        service/phpldapadmin created
        ```

2.  初始化账号。

    1.  登录php-LDAP-admin。

        1.  执行以下命令获取ingress域名和地址。

            ```
            01_ldap_with_ui git:(master) ✗ kubectl get ing
            ```

            ```
            NAME      HOSTS                                                                   ADDRESS          PORTS   AGE
            ldap-ui   phpldap.c26a7ab225d1544088735677ed906xxxx.cn-beijing.alicontainer.com   121.xx.xxx.xxx   80      45s
            ```

        2.  拷贝上述获取的ingress域名至浏览器中，然后用默认的DN和密码登录php-LDAP-admin控制台页面。

            **说明：**

            -   默认的DN：cn=admin,dc=example,dc=org
            -   默认的密码：admin
    2.  创建组织和账号。

        1.  在php-LDAP-admin控制台左侧导航栏，单击**Create new entry here**，在Create Object页面，单击**Generic: Organisational Unit**，然后单击**Create Object**。

            ![unit](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8463659951/p141650.png)

        2.  输入组织名称，然后单击**Create Oject**。本文组织示例名称为dev。
        3.  在php-LDAP-admin控制台左侧导航栏，单击上步中创建的ou=dev，单击**Create a child entry**，然后单击**Courier Mail：Account**，配置创建用户信息，然后单击**Create Object**。

            ![Account](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9463659951/p141660.png)

            **说明：** php-LDAP-admin的账号默认生成的名字中间会有空格，请注意删除空格。


## 步骤二：配置IDaaS

配置IDaaS，导入组织和账号数据的步骤如下。

1.  登录[应用身份管理控制台](https://yundun.console.aliyun.com/?spm=5176.cnidaas.0.0.1f68782bpjiAoX&p=idaas#/instanceList/cn-hangzhou)。

2.  在实例列表中，单击目标实例名称。

3.  在左侧导航栏中，选择**账号** \> **机构及组**。

4.  在**查看详情**区域，单击**配置LDAP**。

5.  在**LDAP配置**面板中，单击**新建配置**。

6.  填写LDAP配置的相关信息。有关配置项的详细信息，请参见[LDAP账户同步配置]()。

    LDAP部分配置信息说明如下表。

    -   服务器链接

        |参数|描述|
        |--|--|
        |AD/LDAP名称|自定义名称。|
        |服务器地址|LDAP服务对外的服务地址（非web页面地址）。|
        |端口号|LDAP服务对外的服务端口号，设置为389。|
        |Base DN|设置LDAP的同步目录。|
        |管理员DN|有LDAP管理权限的管理员账号。|
        |密码|LDAP管理员密码。|
        |类型|设置为**Windows AD**或**OpenLDAP**。|
        |LDAP同步至本系统|需设置为**启用**。|
        |本系统同步至LDAP|可设置为**禁用**或**启用**。|

    -   字段匹配规则

        |参数|描述|
        |--|--|
        |用户名|LDAP中的用户名定义，一般为CN。|
        |外部ID|        -   如果是Windows AD，设置为objectGUID。
        -   如果是OpenLdap，设置为uid。 |
        |密码字段|        -   如果是Windows AD，设置为unicodePwd。
        -   如果是OpenLdap，设置为userPassword。 |
        |用户唯一标识|        -   如果是Windows AD，设置为DistinguishedName。
        -   如果是OpenLdap，设置为EntryDN。 |
        |邮箱|填写字段名，如mail。|
        |手机号|填写字段名，如telephoneNumber。|
        |昵称|在IDaaS中账户的显示名称。|
        |默认密码|定时从AD或者LDAP同步账户到IDaaS系统时的默认密码。|

    **说明：**

    LDAP配置说明如下：

    -   LDAP配置中**账号**区域的**管理员DN**和**密码**为：
        -   管理员DN：cn=admin,dc=example,dc=org
        -   密码：admin
    -   服务器链接和字段匹配规则配置完后，需分别单击**测试连接**，然后单击**保存**。
    -   如果您只想同步部分的账号到阿里云，可以在**Base DN**处填写需要同步的根目录即可；如果是多个非同一个根目录的部门需要同步，重复进行LDAP配置可以设置多个数据源。
7.  导入组织与账号。具体操作步骤请参见[从AD导入账户及组织机构]()。


## 步骤三：配置IDaaS单点登录

1.  创建应用。

    1.  在实例详情页左侧导航栏，选择**应用** \> **添加应用**。

    2.  在**添加应用**页面的**应用ID**列下，选择**plugin\_aliyun\_role** ，然后单击其右侧的**添加应用**。

    3.  在弹出的面板中，单击**添加SigningKey**，填写相关信息，然后单击**提交**。

    4.  单击目标应用右侧的**选择**进行LDAP配置。

        **说明：** 此处的AK信息填写为创建有AliyunRAMFullAccess权限的RAM子账户信息。

    5.  单击**提交**。

2.  按照组织授权应用。

    1.  在实例详情页左侧导航栏，选择**授权** \> **应用授权**。

    2.  在**按应用授权组织机构/组**页签，单击目标应用和目标组织机构。

    3.  在实例详情页左侧导航栏，选择**应用** \> **应用列表**，然后单击应用右侧的**详情**，在**应用信息**区域，单击**查看详情**。

    4.  在**应用详情**面板中，单击**导出IDaaS SAML元配置文件**。

    5.  在[RAM控制台](https://ram.console.aliyun.com/overview)左侧导航栏，单击**SSO管理**，然后在**角色SSO**页签，单击**新建身份提供商**。

    6.  在**新建身份提供商**面板，填写提供商名称，单击**上传文件**上传元配置文件，然后单击**确定**。

        ![SSO](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9463659951/p141774.png)

    7.  创建RAM角色为**身份提供商**并赋权。具体步骤请参见[创建可信实体为身份提供商的RAM角色](/intl.zh-CN/角色管理/创建RAM角色/创建可信实体为身份提供商的RAM角色.md)。

    8.  在[应用身份管理控制台](https://yundun.console.aliyun.com/)左侧导航栏，选择**应用** \> **应用列表**，在目标应用右侧，单击**详情**，然后在**账户信息 - 子账户**区域，单击**查看应用子账户**。

    9.  在**子账户**页面，单击**添加账号关联**。

    10. 在**添加账号关联**面板，填写主账户和子账户信息，然后单击**保存**。

        **说明：**

        主账号为**机构及组**页面的**账户**页签中导入的LDAP用户的名称。

        子账号登录后的权限，可通过绑定role或者账号获得，可通过文件批量设置子账号对应角色授权ACK的fullAccess权限。


## 步骤四：为单点登录配置认证源IDaaS

1.  在[应用身份管理控制台](https://yundun.console.aliyun.com/)左侧导航栏，选择**认证** \> **认证源**。

2.  在**认证源**页面右上角，单击**添加认证源**。

3.  选择**LDAP**认证源并单击右侧的**添加认证源**。

4.  填写配置信息，然后单击**提交**。

    **说明：** 选中**是否显示**，否则认证源不会在登录框部分显示。有关更多配置信息，请参见[LDAP认证登录]()。


## 步骤五：查看效果

通过单点登录页面的链接查看效果。

1.  在[应用身份管理控制台](https://yundun.console.aliyun.com/)左侧导航栏，选择目标实例。

2.  将目标实例右侧的用户登录页地址拷贝至浏览器，然后单击LDAP认证源。

    有关LDAP认证登录的更多信息，请参见[LDAP认证登录]()。


