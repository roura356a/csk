---
keyword: [安装云原生AI套件, 配置AI-Dashboard]
---

# 安装云原生AI套件

云原生AI套件只支持安装在ACK Pro版集群中。本文介绍如何在ACK Pro版集群中安装云原生AI套件，以及如何配置AI-Dashbaord。

-   已创建ACK Pro版集群，且在组件配置页面，需要选中**监控插件**和**日志服务**。具体操作，请参见[创建ACK Pro版集群](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/创建ACK Pro版集群.md)。
-   ACK Pro托管版集群的Kubernetes版本不低于1.18。

## 部署云原生AI套件

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**应用** \> **AI工程加速**。

5.  在**云原生AI套件**页面，单击**一键部署**。

6.  在一键部署云原生AI套件页面，根据需要选中相应的组件后，单击页面下方的**部署云原生AI套件**，开始检查环境和依赖项，检查通过后，自动部署选择的组件。

    **说明：**

    -   **交互方式**下的**Arena命名行（必选）**为默认必选组件。
    -   选中**AI-Dashboard控制台**时，会弹出**提示**话框。具体操作，请参见步骤[1](#step_twc_y20_m4t)。
    组件安装成功后，在组件列表页面：

    -   您能看到当前集群中已经安装的组件名称、版本等信息，并能对组件进行**部署**、**卸载**操作。
    -   如果已安装的组件有新版本的话，还可以对组件进行**升级**操作。
    -   安装了AI-Dashboard组件后，您可在页面左上方看到AI-Dashboard访问地址，通过该地址可以访问AI-Dashboard控制台页面。

## 安装配置AI-Dashboard控制台

1.  在云原生AI套件一键部署页面选中**AI-Dashboard控制台**时，会弹出下图所示的**提示**对话框。

    ![K-AI-2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8804787161/p237448.png)

    **说明：**

    -   AI-Dashboard控制台使用**公网域名**访问时，请注意控制访问范围。
    -   AI-Dashboard控制台选择**私网**网络类型访问时，在**提示**对话框中选中**私网IP**。
2.  单击**提示**对话框中的授权策略链接。

    1.  在**RAM角色管理**页面中，选择**权限管理**页签，单击目标授权策略名称。

    2.  **策略内容**页签中，单击**修改策略内容**，在**修改策略内容**面板的**策略内容**区域，将以下策略代码添加至`Action`字段中。

        ```
        "ecs:DescribeInstances",
        "ecs:DescribeSpotPriceHistory",
        "ecs:DescribePrice",
        "eci:DescribeContainerGroups",
        "eci:DescribeContainerGroupPrice",
        "log:GetLogStoreLogs",
        "ims:CreateApplication",
        "ims:GetApplication",
        "ims:ListApplications",
        "ims:DeleteApplication",
        "ims:CreateAppSecret",
        "ims:GetAppSecret",
        "ims:ListAppSecretIds"
        ```

    3.  添加完成后，单击**确定**。

3.  在一键部署**云原生AI套件**页面选中**监控组件**。

    ![K-AI-3](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1931772161/p237487.png)

4.  单击**部署云原生AI套件**。


