---
keyword: [k8s, kubectl, 临时kubeconfig, kubeconfig]
---

# 通过kubectl管理Kubernetes集群

如果您需要从客户端计算机连接到Kubernetes集群，可以使用Kubernetes命令行客户端kubectl。本文主要为您介绍如何通过kubectl连接Kubernetes集群。

关于kubectl命令行的更多信息，请参见[kubectl](https://kubernetes.io/docs/user-guide/kubectl/)。

## 通过kubectl连接Kubernetes集群

1.  安装和设置kubectl客户端。

    有关详细信息，请参见[安装和设置kubectl](https://kubernetes.io/docs/tasks/kubectl/install/)。

2.  配置访问集群的凭据。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在控制台左侧导航栏中，单击**集群**。

    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

    4.  在控制台左侧导航栏中，单击**集群信息**。

    5.  在**集群信息**页面，单击**连接信息**页签。

        ![kubeconfig](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6785959161/p269156.png)

    6.  配置访问凭证kubeconfig。

        -   如果您使用公网访问，请选择**公网访问**页签，并单击**复制**，将内容复制到本地计算机的$HOME/.kube/config（即kubeconfig文件所在的位置）。

            **说明：** kubectl默认会从$HOME/.kube/config目录下查找文件名为config的文件。kubectl通过kubeconfig的配置与集群产生交互。

        -   如果您使用内网访问，请选择**内网访问**页签，并单击**复制**，将内容复制到本地计算机的`$HOME/.kube/config`（即kubeconfig所在位置）。
    **执行结果**

    -   如果您创建的是ACK托管版集群，在kubeconfig文件配置完成后，您可以使用kubectl命令从计算机访问Kubernetes集群。

    -   如果您创建的是ACK专有版集群，您可以在**基本信息**页签查看集群Master节点的SSH连接地址，然后使用kubectl命令通过SSH连接地址从计算机访问Kubernetes集群。

        ![集群链接地址](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9943170261/p9791.png)

        **说明：** 创建专有版集群时，需选中**SSH登录**才能使用SSH访问集群。关于SSH的详细配置，请参见[创建Kubernetes专有版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes专有版集群.md)。


## 生成临时的kubeconfig文件

为了保证集群连接的安全性，您可以生成临时kubeconfig文件，并设置文件使用的有效期，以正常使用临时的kubeconfig文件连接ACK集群。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在**集群信息**页面，单击**连接信息**页签，然后单击**生成临时KubeConfig**。

5.  在**临时KubeConfig**对话框中，设置临时kubeconfig文件的有效期及访问集群的方式（包括公网访问和内网访问）。

    ![tempo](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6785959161/p269169.png)

    -   如果您使用公网访问，请选择**公网访问**，单击**生成临时KubeConfig**，然后单击**复制**，将内容复制到本地计算机的`$HOME/.kube/config`文件下（`kubectl`预期凭据所在的位置）。
    -   如果您使用内网访问，请选择**内网访问**，单击**生成临时KubeConfig**，然后单击**复制**，将内容复制到本地计算机的`$HOME/.kube/config`文件下（`kubectl`预期凭据所在的位置）。

## 吊销kubeconfig文件

您可以吊销集群的kubeconfig文件，以保障集群的安全。具体操作，请参见[吊销集群的KubeConfig凭证](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/吊销集群的KubeConfig凭证.md)。

**相关文档**  


[获取集群kubeconfig接口](/intl.zh-CN/API参考/集群/获取集群kubeconfig接口.md)

