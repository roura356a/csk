---
keyword: [CloudShell, Kubernetes集群, K8s]
---

# 在CloudShell上通过kubectl管理Kubernetes集群

CloudShell是阿里云推出的云命令行工具，您可以使用CloudShell工具在任意浏览器上运行CloudShell命令管理阿里云资源。本文为您介绍如何在容器服务ACK控制台上利用CloudShell通过kubectl管理集群。

CloudShell仅支持通过公网连接集群，因此待连接集群的API Server必须已开启公网访问能力。具体操作，请参见[控制集群API Server的公网访问能力](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过公网访问集群API Server.md)。

如果您希望通过kubectl工具管理容器服务ACK中的Kubernetes集群，您可下载kubectl到客户端（具体操作，请参见[通过kubectl工具连接集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl工具连接集群.md)）。您也可以在容器服ACK控制台上打开CloudShell，系统会自动加载集群的KubeConfig文件，您无需任何安装或配置操作，可以直接在CloudShell中通过kubectl管理集群。

Cloud Shell是网页版命令行工具。您可以在任意浏览器上运行Cloud Shell管理阿里云资源。在Cloud Shell启动时会自动为您分配一台Linux虚拟机供您免费使用。在该虚拟机上预装Python、Java、Node.js环境和Aliyun CLI、cURL、SSH、kubectl、Fun、Terraform、Ansible、Vim等多种云管理工具和系统工具，配合Cloud Shell提供的[Web IDE](https://docs.gitlab.com/ee/user/project/web_ide/)，辅助您完成日常的云资源管理。更多信息，请参见[什么是云命令行？]()。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群右侧**操作**列下的**更多** \> **通过CloudShell管理集群**。

4.  单击![cloudshell.png](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/0475659951/p141958.png)，然后单击挂载存储空间。

    关联并挂载一个NAS文件系统实例可以持久化存储您的常用脚本和文件，否则您的文件会随实例释放而销毁。可根据您的实际情况单击**创建并绑定**或**暂不创建**。


在CloudShell中可通过kubectl工具管理ACK集群。

**说明：** 在打开集群关联的CloudShell时，系统会自动加载集群的kubeconfig文件。您可以通过kubectl直接管理您的集群。

例如，执行以下命令，查看当前集群的Pod。

```
kubectl get pod
```

预期输出：

```
NAME                             READY   STATUS    RESTARTS   AGE
nginx-dynamic-5b4bdb64c4-gxqs5   1/1     Running   0          178m
web-0                            1/1     Running   0          34d
web-1                            1/1     Running   0          34d
```

![管理集群2](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/0475659951/p34727.png)

**相关文档**  


[使用云命令行]()

[云命令行支持工具列表]()

[上传和下载文件]()

