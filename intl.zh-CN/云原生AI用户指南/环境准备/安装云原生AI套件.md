---
keyword: [安装云原生AI套件, 云原生AI运维控制台, 云原生AI开发控制台]
---

# 安装云原生AI套件

云原生AI套件只支持安装在ACK Pro版集群中。本文介绍如何在ACK Pro版集群中安装云原生AI套件，以及如何安装配置云原生AI运维控制台和开发控制台。

-   已创建ACK Pro版集群，且在组件配置页面，需要选中**监控插件**和**日志服务**。具体操作，请参见[创建ACK Pro版集群](/intl.zh-CN/Kubernetes集群用户指南/ACK Pro集群/创建ACK Pro版集群.md)。

    **说明：** 该前提条件仅是安装配置云原生AI运维控制台的前提条件。

-   ACK Pro版集群的Kubernetes版本不低于1.18。

## 部署云原生AI套件

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**应用** \> **AI工程加速（公测）**。

5.  在**云原生AI套件**页面，单击**一键部署**。

6.  在一键部署云原生AI套件页面，根据需要选中相应的组件后，单击页面下方的**部署云原生AI套件**，开始检查环境和依赖项，检查通过后，自动部署选择的组件。

    **说明：**

    -   **交互方式**下的**Arena命名行（必选）**为默认必选组件。
    -   选中**运维控制台**时，会弹出**提示**话框。后续具体操作，请参见[安装配置云原生AI运维控制台](#section_93j_4nk_ojr)。
    -   选中**开发控制台**时，同样会弹出**提示**话框。后续具体操作，请参见[安装配置云原生AI开发控制台](#section_oj7_fkl_2tc)。
    组件安装成功后，在组件列表页面：

    -   您能看到当前集群中已经安装的组件名称、版本等信息，并能对组件进行**部署**、**卸载**操作。
    -   如果已安装的组件有新版本的话，还可以对组件进行**升级**操作。
    -   安装了云原生AI运维控制台组件（**ack-ai-dashboard**）或云原生AI开发控制台组件（**ack-ai-dev-console**）后，您可在**云原生AI套件**页面左上方看到**运维控制台**或**开发控制台**链接，单击可以直接访问相应的控制台页面。

## 安装配置云原生AI运维控制台

1.  在云原生AI套件一键部署页面中**交互方式**选中**运维控制台**时，会弹出下图所示的**提示**对话框。

    ![K-AI-2](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/5571550361/p237448.png)

    **说明：**

    -   云原生AI运维控制台使用**公网域名**访问时，请注意控制访问范围。
    -   云原生AI运维控制台选择**私网**网络类型访问时，在**提示**对话框中选中**私网IP**。
    -   控制台数据存储方式选择**集群内置MySQL**时，数据安全无SLA保障，操作集群时请谨慎。
2.  单击**提示**对话框中的授权策略链接。

    1.  在**RAM角色管理**页面中，选择**权限管理**页签，单击目标授权策略名称。

    2.  **策略内容**页签中，单击**修改策略内容**，在**修改策略内容**面板的**策略内容**区域，将以下策略代码添加至`Action`字段中。

        ```
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
        "ims:ListAppSecretIds",
        "ims:ListUsers"
        ```

    3.  添加完成后，单击**确定**。

3.  选择控制台数据存储方式。

    -   当选择**集群内置MySQL**时，需要确保集群节点挂载有ESSD类型的云盘。云原生AI套件会自动在集群内安装部署MySQL，该方式不需要再单独购买RDS产品，但数据安全没有SLA保障。如果集群故障或误删MySQL，可能导致数据丢失。
    -   当选择**阿里云RDS**时，需要单独购买阿里云RDS，并使用以下YAML示例模板在集群kube-ai命名空间下创建名为kubeai-rds的Secret，Secret中需要包含RDS地址、数据库名字、用户名、密码。

        ```
        apiVersion: v1
        kind: Secret
        metadata:
          name: kubeai-rds
          namespace: kube-ai
        type: Opaque
        stringData:
          MYSQL_HOST: "Your RDS URL"
          MYSQL_DB_NAME: "Database name"
          MYSQL_USER: "Database username"
          MYSQL_PASSWORD: "Database password"
        ```

        **说明：**

        -   如果需要修改数据存储方式，需要卸载云原生AI套件，再重新安装。如果集群中有kubeai-rds Secret，需要通过kubectl一并删除。
4.  在一键部署**云原生AI套件**页面选中**监控组件**。

    ![K-AI-3](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/0771859161/p237487.png)

5.  单击**部署云原生AI套件**。


## 安装配置云原生AI开发控制台

-   如果您未安装过云原生AI套件的任何组件，在**云原生AI套件**页面，单击**一键部署**。
    1.  **交互方式**选中**开发控制台**，弹出下图所示的**提示**对话框。请根据**提示**中的配置步骤完成授权，并选择访问方式。关于授权的具体操作，请参见本文安装配置云原生AI运维控制台的步骤2。

        **说明：** **交互方式**下的**Arena命名行（必选）**为默认必选组件。

        ![A33](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/4971381261/p275061.png)

    2.  单击**部署云原生AI套件**。
-   如果您已安装过云原生AI套件的某些组件，在**云原生AI套件**页面的组件列表中，单击**ack-ai-dev-console**组件右侧**操作**列下的**部署**。

