# 在CloudShell上通过kubectl管理Kubernetes集群 {#task_y24_gbm_2gb .task}

本文为您介绍如何在容器服务Kubernetes控制台上利用CloudShell通过kubectl管理Kubernetes集群。

您已成功创建一个Kubernetes集群，参见[创建Kubernetes 集群](cn.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes 集群.md#)。

若您希望通过kubectl工具管理容器服务Kubernetes集群，您可下载kubectl到客户端，具体安装方法，请参见[通过 kubectl 连接 Kubernetes 集群](cn.zh-CN/Kubernetes集群用户指南/集群管理/管理与访问集群/通过 kubectl 连接 Kubernetes 集群.md#)。您也可以直接在容器服务Kubernetes控制台上打开CloudShell，在CloudShell中通过kubectl管理集群。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏的**集群** \> **集群**，进入集群列表页面。
3.  选择目标集群，单击**操作**列**更多** \> **通过CloudShell管理集群**。 

    ![管理集群](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/81209/156751307834720_zh-CN.png)

    **说明：** 

    打开后，请执行以下操作：

    -   在授权页面单击**确认**，可获取一个临时的会话访问秘钥，秘钥有效期为1小时。
    -   在挂载存储空间页面，可根据您的实际情况单击**创建并绑定**或**暂不创建**。
4.  在CloudShell中可通过kubectl工具管理容器服务Kubernetes集群。 

    **说明：** 在打开集群关联的CloudShell时，系统会自动加载集群的kubeconfig文件。您可以通过kubectl直接管理您的集群。

    ![管理集群2](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/81209/156751307934727_zh-CN.png)


