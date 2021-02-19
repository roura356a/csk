# 在Eclipse中安装Cloud Toolkit插件并部署应用到容器服务Kubernetes

本文档将向您介绍如何在Eclipse中安装Cloud Toolkit，并使用Cloud Toolkit快速部署一个应用。

Alibaba Cloud Toolkit（下文简称Cloud Toolkit）是一个免费的IDE插件，帮助阿里云用户更高效地使用阿里云。

您只需注册或使用一个已有的阿里云账号，即可免费下载Cloud Toolkit。下载完成后，您可以将该插件安装到Eclipse中。

当您在本地完成应用程序的开发、调试及测试后，通过该插件即可轻松将应用程序部署到阿里云。关于Cloud Toolkit的详细信息，请参见[Alibaba Cloud Toolkit](https://help.aliyun.com/product/29966.html)。

## 安装Cloud Toolkit

您可以在如下路径下获取软件安装包。

-   下载并安装[JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)或更高版本。
-   下载并安装适用于Java EE开发人员的[Eclipse IDE、4.5.0（代号：Mars）或更高版本](https://www.eclipse.org/downloads/)。

有两种安装Cloud Toolkit方式：在Eclipse Marketplace中下载安装和直接通过Cloud Toolkit的官方地址安装。

-   在Eclipse Marketplace中下载安装。
    1.  启动Eclipse。
    2.  在菜单栏中选择**Help** \> **Eclipse Marketplace...**。
    3.  在Eclipse Marketplace对话框中**Find**右侧的文本框中输入**Alibaba Cloud Toolkit**，然后回车。
    4.  单击搜索结果中**Alibaba Cloud Toolkit**右下角的**Installed**。

        ![安装插件1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7563659951/p33265.png)

    5.  按照Eclipse安装页面的提示，完成后续安装步骤。
-   通过Cloud Toolkit的官方地址安装。

    **说明：** 如果您无法连接Eclipse Market服务器，请选择这种安装方式。

    1.  启动Eclipse。
    2.  在菜单栏中选择**Help** \> **Install New Software**。
    3.  在Available Software对话框的**Work with**文本框输入Cloud Toolkit for Eclipse的URL：http://toolkit.aliyun.com/eclipse/。
    4.  在下面的列表区域中选中需要的组件`Alibaba Cloud Toolkit Core`和`Alibaba Cloud Toolkit Deployment Tools`，并在下方的Details区域中不选中`Contact all update sites during install to find required software。`

        ![安装插件2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6593463161/p33267.png)

    5.  配置完成后，单击**Next**，Eclipse开始安装插件，并显示安装进度。

        ![安装插件3](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8563659951/p33268.png)

    6.  按照Eclipse安装页面的提示，完成后续安装步骤。

预期结果：

插件安装成功后，重启Eclipse，您可以在工具栏看到Alibaba Cloud Toolkit的图标。

![插件安装完成](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8563659951/p33269.png)

## 获取AccessKey ID和AccessKey Secret

您本地的应用部署到云端时，都需要使用阿里云上的资源、应用。所以在部署前，需要设置您的阿里云账号信息，以保证拥有使用、管理、应用相关资源的权限。

-   使用阿里云主账号获取AccessKey ID和AccessKey Secret
    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
    2.  将光标滑动（非单击）到控制台页面右上角您的头像上，在弹出的下拉菜单中单击**AccessKey 管理**。

        ![获取ak](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7593463161/p33270.png)

    3.  在安全提示对话框中单击**继续使用AccessKey**。

        ![安全提示](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8563659951/p33271.png)

    4.  在安全信息管理页面，用户AccessKey区域右侧单击**创建AccessKey**，在手机验证对话框中单击**点击获取**后输入验证码。
    5.  记录该账号的AccessKey ID和AccessKey Secret。
-   使用RAM子账号获取AccessKey ID和AccessKey Secret
    1.  登录[RAM子账号登录页面](https://signin.aliyun.com/login.htm)，输入您的子账号，单击**下一步**，再输入密码，单击**登录**。
    2.  将光标滑动（非单击）到控制台页面右上角您的头像上，在弹出的下拉菜单中单击**AccessKey管理**。

        ![ak管理](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7593463161/p33273.png)

    3.  在安全信息管理页面，用户AccessKey区域右侧单击**创建AccessKey**。

        **说明：**

        -   如果您当前子账号的**创建AccessKey**置灰不可用，请使用主账号对该子账号授权，可参见[RAM授权](/cn.zh-CN/Kubernetes集群用户指南/授权管理/授权概述.md)。
        -   在新建用户AccessKey对话框中，单击AccessKey详情右侧下拉箭头，记录当前子账号的**AccessKeyID**和**AccessKeySecret**。
        -   这是用户AccessKey可供下载的唯一机会，请及时保存。

## 设置AccessKey ID和AccessKey Secret

1.  启动Eclipse。

2.  在工具栏单击**AlibabaCloud Toolkit Preferences**图标，在下拉菜单中单击**Preferences...**。

3.  在Preferences \(Filtered\)对话框的左侧导航栏中单击**Accounts**。

4.  在Accounts区域设置**AccessKey ID**和**AccessKey Secret**，然后单击**OK**。

    ![获取ak密钥](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8563659951/p33310.png)

    **说明：**

    -   如果您已有阿里云账号，单击**Get existing AK/SK**， 参见文档获取**AccessKey ID**和**AccessKey Secret**。
    -   如果您没有阿里云账号，单击**Sign up**，进入阿里云账号注册页面，注册账号。注册完成后按照上述方式获取**AccessKey ID**和**AccessKey Secret**。

## 部署应用

1.  您已经完成部署前准备工作。

    -   您已使用设置**AccessKey ID**和**AccessKey Secret**的阿里云账号进入[容器镜像服务控制台](https://cr.console.aliyun.com/cn-hangzhou/new)，创建容器镜像仓库，可参见[仓库构建](https://help.aliyun.com/document_detail/60997.html)。
    -   您已使用设置**AccessKey ID**和**AccessKey Secret**的阿里云账号进入[容器服务管理控制台](https://cs.console.aliyun.com)，并使用镜像创建应用，可参见[使用镜像创建无状态Deployment应用](/cn.zh-CN/Kubernetes集群用户指南/应用管理/使用镜像创建无状态Deployment应用.md)或[使用镜像创建有状态StatefulSet应用](/cn.zh-CN/Kubernetes集群用户指南/应用管理/使用镜像创建有状态StatefulSet应用.md)。
2.  设置用于打包本地镜像的Docker环境。

    1.  在Eclipse工具栏单击**AlibabaCloud Toolkit Preferences**， 在下拉菜单中单击**Preferences...**。

    2.  在Preferences \(Filtered\)对话框的左侧导航栏中单击**Docker**。

    3.  在Docker界面中设置可连接的Docker环境，包括本地和远程两种方式。

        -   本地Docker环境
            -   如果您本地为Mac或Linux操作系统，勾选**Unix Socket**后单击**Browse...**，选择本地的Docker安装目录。
            -   如果您本地为Windows操作系统，勾选**Tcp Connection**后在URI右侧文档框输入本地Docker的URI，如`http://127.0.0.1:2375`。
        -   远程Docker环境

            选中**Tcp Connection**后在URI右侧文档框输入远端Docker环境的URI（包括IP地址和端口），如`http://127.0.0.1:2375`，并确保远程主机的HTTP服务开启。

            ![远程docker环境](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8563659951/p33347.png)

    4.  配置然完成后单击**OK**。

3.  在Eclipse界面左侧的Package Explorer中右键单击您的Docker应用工程名，在弹出的下拉菜单中选择**Alibaba Cloud** \> **Deploy to CS Kubernetes...**。

4.  在Select a package method对话框选择本地应用程序的**Context Directory**和**Dockerfile**（通常会根据您本地的应用工程自动识别并设置 ），然后单击**Next**。

    **说明：** 您可以根据您的需要决定是否勾选**Use maven build**使用Maven构建应用工程。

    ![选择路径](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8563659951/p33348.png)

5.  在Select a Repository对话框选择容器镜像服务的地域，命令空间和镜像仓库，然后单击**Next**。

    **说明：** 如果您还没有镜像仓库，在对话框右上角单击**Create a new repository**跳转到容器镜像仓库创建镜像仓库。创建步骤可参见[容器镜像服务](https://help.aliyun.com/product/60716.html)文档。

    ![镜像仓库](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8563659951/p33349.png)

6.  在Deploy Project to CS Kubernetes对话框选择容器服务Kubernetes的**Cluster**、**Namespace**和**Deployment**，然后单击**Finish**。

    **说明：**

    -   如果您还没有创建容器服务Kubernetes的Deployment，在对话框右上角单击**Create a new Kubernetes deployment**，跳转到[容器服务管理控制台](https://cs.console.aliyun.com)创建Deployment。创建步骤可参见[容器服务Kubernetes版](https://help.aliyun.com/product/85222.html)文档。
    -   Alibaba Cloud Toolkit目前只支持以更新方式部署您的应用，需要您预先在控制台手动创建一个部署（Deployment），然后基于该部署将镜像替换为您的镜像。
    ![创建完成](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8563659951/p33350.png)


预期结果：

部署开始后，Eclipse的Console区域会打印部署日志，您可以根据日志信息检查部署结果。 更多信息请参见[使用Eclipse部署应用到容器服务Kubernetes]()。

**说明：** 如果您在使用Cloud Toolkit过程中有任何疑问，欢迎您扫描下面的二维码加入钉钉群进行反馈。

![钉钉群二维码4群](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8390919951/p99762.png)

