# 通过 kubectl 连接 Kubernetes 集群 {#task_ubf_lhg_vdb .task}

本文主要为您介绍如何通过 kubectl 连接 Kubernetes 集群。

如果您需要从客户端计算机连接到 Kubernetes 集群，请使用 Kubernetes 命令行客户端 [kubectl](https://kubernetes.io/docs/user-guide/kubectl/)。

1.  从[Kubernetes 版本页面](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG.md) 下载最新的 kubectl 客户端。
2.  安装和设置 kubectl 客户端。 有关详细信息，参见 [安装和设置 kubectl](https://kubernetes.io/docs/tasks/kubectl/install/)。
3.  配置集群凭据。 

    -   如果您使用公网访问，请选择**KubeConfig（公网访问）**页签，并单击**复制**，将内容复制到本地计算机的 `$HOME/.kube/config`（`kubectl` 预期凭据所在的位置）。
    -   如果您使用内网访问，请选择**KubeConfig（内网访问）**页签，并单击**复制**，将内容复制到本地计算机的 `$HOME/.kube/config`（`kubectl` 预期凭据所在的位置）。
    配置完成后，您可以使用 kubectl 从计算机访问 Kubernetes 集群。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
    2.  单击**Kubernetes**进入 Kubernetes 集群列表页面。
    3.  选择所需的集群并单击右侧的**管理**。 

        您可以在**连接信息**处查看集群的连接地址。

        ![集群链接地址](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16645/15682589309791_zh-CN.png)

        **说明：** 本操作仅适用专有版Kubernetes集群。


