---
keyword: [Knative组件, 部署]
---

# 部署Knative组件

Knative部署完成之后，如果需要对未安装的组件进行部署时，可以在**组件管理**页面，可以选择未部署的组件进行部署操作。ACK支持部署核心组件以及add-on组件。本文介绍如何部署Knative组件。

-   [创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)
-   [部署Knative](/intl.zh-CN/Kubernetes集群用户指南/Knative/Knative组件管理/一键部署Knative.md)

您部署Knative时，如果您未选中需要部署的组件，您可以按照本文操作。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**应用** \> **Knative**。

5.  在**状态**为**未部署**的组件右侧，单击**部署**。

    ![部署组件](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3895659951/p48920.png)

6.  在弹出的对话框中，单击**确定**。


部署完成后，在Knative组件管理页面，可以看到当前组件状态为已部署。

![部署结果](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3895659951/p48922.png)

**相关文档**  


[一键部署Knative](/intl.zh-CN/Kubernetes集群用户指南/Knative/Knative组件管理/一键部署Knative.md)

[升级组件](/intl.zh-CN/Kubernetes集群用户指南/Knative/Knative组件管理/升级组件.md)

