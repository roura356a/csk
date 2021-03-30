---
keyword: [Helm, 发布管理]
---

# 基于Helm的发布管理

阿里云Kubernetes服务集成了Helm包管理工具，帮助您快速构建云上应用，因为chart可以多次发布（release），这就带来一个发布版本管理的问题。因此，阿里云Kubernetes服务提供了发布功能，通过Web界面的方式对通过Helm发布的应用进行管理。

-   您已经成功创建一个Kubernetes集群，参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   您已经使用应用目录或服务目录功能，安装了Helm应用，参见[利用Helm简化应用部署](/intl.zh-CN/Kubernetes集群用户指南/应用/应用调度部署/使用Helm简化应用部署.md)。本例中是一个tf-model应用。

## 查看发布的详情

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**应用** \> **Helm**。

5.  以tf-model为例，您可查看发布的详情信息，单击右侧的**详情**，进入该发布的详情页面。

    您可以查看该发布的当前版本和历史版本等信息，当前版本为1，无历史版本。您还可查看tf-model的资源信息，如资源名称、资源类型，以及查看YMAL信息。

    **说明：** 单击资源名称，可进入Kubernetes Dashboard页面，查看对应资源的详细运行状态。

    ![发布详情页面](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8295659951/p14017.png)

6.  单击**参数**页签，您可查看该Helm包安装的参数配置。

    ![参数配置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8295659951/p14018.png)


## 更新发布的版本

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，单击**发布**。

5.  单击**Helm**页签，进入发布列表页面。

6.  以tf-model为例，您可更新该发布，单击右侧的**更新**，弹出更新发布对话框。

    ![更新](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8295659951/p14019.png)

7.  在更新发布对话框中修改相关参数，随后单击**更新**，可对该发布进行更新。

    ![修改参数](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8295659951/p14020.png)

    在发布列表页面，您可以看到当前版本变为2，您可以在历史版本菜单下找到版本1，单击 **回滚到该版本**，可进行回滚。

    ![回滚版本](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8295659951/p14021.png)


## 删除发布

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，单击**发布**。

5.  单击**Helm**页签，进入发布列表页面。

6.  以tf-model为例，您可删除该发布，单击右侧的**删除**。

7.  在删除应用对话框中，勾选是否**清除发布记录**，然后单击**确定**，您可以删除tf-model应用，其包含的service、deployment等资源都会一并删除。

    ![删除应用](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9295659951/p148067.png)

    **说明：** 如果不勾选**清除发布记录**，此时该发布记录会保留在发布列表中，后续在部署应用时，可能会有重名冲突。


