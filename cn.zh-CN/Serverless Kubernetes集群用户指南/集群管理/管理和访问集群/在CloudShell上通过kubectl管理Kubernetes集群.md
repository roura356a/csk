# 在CloudShell上通过kubectl管理Kubernetes集群

本文为您介绍如何在容器服务Kubernetes控制台上利用CloudShell通过kubectl管理Kubernetes集群。

[创建Serverless Kubernetes集群](/cn.zh-CN/Serverless Kubernetes集群用户指南/快速入门/创建Serverless Kubernetes集群.md)

若您希望通过kubectl工具管理容器服务Kubernetes集群，您可下载kubectl到客户端，具体安装方法，请参见[通过kubectl连接Kubernetes集群](/cn.zh-CN/Serverless Kubernetes集群用户指南/集群管理/管理和访问集群/通过kubectl连接Kubernetes集群.md)。您也可以直接在容器服务Kubernetes控制台上打开CloudShell，在CloudShell中通过kubectl管理集群。

## 操作步骤

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群右侧**操作**列下的**更多** \> **通过CloudShell管理集群**。

    **说明：**

    打开后，请执行以下操作：

    -   在授权页面单击**确认**，可获取一个临时的会话访问密钥，密钥有效期为1小时。
    -   单击![cloudshell.png](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0475659951/p141958.png)，选择挂载存储空间，可根据您的实际情况单击**创建并绑定**或**暂不创建**。
4.  在CloudShell中可通过kubectl工具管理容器服务Kubernetes集群。

    **说明：** 在打开集群关联的CloudShell时，系统会自动加载集群的kubeconfig文件。您可以通过kubectl直接管理您的集群。

    ![管理集群2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0475659951/p34727.png)


