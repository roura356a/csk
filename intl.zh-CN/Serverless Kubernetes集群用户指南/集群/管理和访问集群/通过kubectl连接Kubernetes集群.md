---
keyword: [kubectl, 连接Kubernetes集群]
---

# 通过kubectl连接Kubernetes集群

如果您需要从客户端计算机连接到Kubernetes集群，请使用Kubernetes命令行客户端kubectl。本文介绍如何通过kubectl连接Kubernetes集群。

1.  从[Kubernetes版本页面](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG.md?spm=a2c4g.11186623.2.4.9ZrN6B&file=CHANGELOG.md)下载最新的kubectl客户端。

2.  安装和设置kubectl客户端。

    安装和设置的详细信息，请参见[安装和设置kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)。

3.  配置集群凭据。

    您可以在集群信息页面查看集群凭据。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在控制台左侧导航栏中，单击**集群**。

    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

    4.  选择基本信息页签，您可以在**集群信息**处查看集群的连接地址。

        ![连接集群](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4548649951/p10242.png)

    5.  选择**连接信息**页签，复制集群凭据到本地文件中，您可创建并将集群凭据保存到$HOME/.kube/config（kubectl预期凭据所在的位置）。或者命名一个新的文件，如/tmp/kubeconfig，并执行命令`export KUBECONFIG=/tmp/kubeconfig`。

    6.  执行上述操作后，您可执行以下命令，确认集群连接情况。

        ```
        kubectl get pod
        ```

        ```
        No resources found.
        ```


配置完成后，您可使用kubectl从本地计算机访问Kubernetes集群。

