---
keyword: [K8s, kubectl, 临时kubeconfig, kubeconfig, 集群凭证]
---

# 通过kubectl工具连接集群

除了通过容器服务控制台来管理集群之外，您还可以通过Kubernetes命令行工具kubectl来管理集群以及应用。本文介绍如何通过kubectl客户端连接ACK集群。

Kubectl是标准的Kubernetes命令行管理工具，通过Kubectl您可以连接和管理阿里云容器服ACK的所有集群类型，包括ACK专有版、ACK托管版（包括ACK标准版和ACK Pro版）以及ASK。关于kubectl命令行的更多信息，请参见[kubectl](https://kubernetes.io/docs/user-guide/kubectl/)。

## 步骤一：安装kubectl工具

1.  根据需要，确定安装kubectl的客户端机器。

    您可以通过公网或私网来连接集群，客户端机器的选择建议如下所示：

    -   通过公网连接

        当集群开启了API Server公网访问能力（即集群已通过阿里云弹性公网IP EIP暴露了API Server）时，您可以通过公网中的任一机器作为客户端来连接集群。关于如何开启API Server的公网访问能力，请参见[控制集群API Server的公网访问能力](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过公网访问集群API Server.md)。

    -   通过私网连接

        当您只需要通过私网来连接集群时，kubectl客户端机器必须与集群位于同一VPC。

2.  下载kubectl工具，并安装至对应的客户端机器。

    详细信息，请参见[安装和设置kubectl](https://kubernetes.io/docs/tasks/kubectl/install/)。


## 步骤二：选择集群凭证类型

ACK集群提供了两种集群凭证（即KubeConfig），分别用于公网访问和私网访问。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在**集群信息**页面，单击**连接信息**页签，根据需要选择公网或私网访问凭证。

    **说明：** 为了实现更灵活的集群连接策略，您还可以生成临时KubeConfig，以控制凭证的有效期。详情请参见[生成临时的KubeConfig](#section_4ps_hwr_03y)。

    ![kubeconfig](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/6785959161/p269156.png)


## 步骤三：配置集群凭证

kubectl工具默认会从客户端机器的$HOME/.kube目录下查找名为config的文件，该文件用于存储所要管理集群的访问凭证，kubectl会根据该配置文件连接至集群。

1.  选择**公网访问**或**私网访问**页签，单击**复制**。

2.  将复制的集群凭证内容粘贴至$HOME/.kube目录下的config文件中，保存并退出。

    **说明：** 如果$HOME/目录下没有.kube目录和config文件，请自行创建。

    如果您使用的是专有版集群并且集群开启了公网SSH登录时，在**连接信息**页签下会显示**SSH**页签。除了参照以上方式连接集群外，您还可以通过SSH登录Master节点，配置集群访问凭证后，即可使用kubectl管理集群。关于如何通过SSH方式连接专有版集群，请参见[通过SSH连接ACK专有版集群的Master节点](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过SSH访问Kubernetes集群.md)。

    ![专有版集群](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/2642318261/p301443.png)


## 步骤四：验证集群连通性

集群凭证配置之后，您可以执行kubectl命令以验证集群的联通性。以查询命名空间为例，执行以下命令。

```
kubectl get namespace
```

预期输出：

```
NAME              STATUS   AGE
default           Active   4h39m
kube-node-lease   Active   4h39m
kube-public       Active   4h39m
kube-system       Active   4h39m
```

## 生成临时的KubeConfig

为了实现更灵活的集群连接策略，您可以生成临时集群凭证（KubeConfig），并设置凭证使用的有效期。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在**集群信息**页面，单击**连接信息**页签，然后单击**生成临时KubeConfig**。

5.  在**临时KubeConfig**对话框中，设置临时凭证的有效期及访问集群的方式（包括公网访问和内网访问）。

    ![tempo](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/6785959161/p269169.png)

    -   如果您使用公网访问，请选择**公网访问**，单击**生成临时KubeConfig**，然后单击**复制**，将内容粘贴到本地计算机的`$HOME/.kube/config`文件下。
    -   如果您使用内网访问，请选择**内网访问**，单击**生成临时KubeConfig**，然后单击**复制**，将内容粘贴到本地计算机的`$HOME/.kube/config`文件下。
    之后便可通过临时凭证连接至集群。当临时凭证过期后，将无法连接集群。


## 吊销KubeConfig

您可以吊销集群的KubeConfig，以保障集群的安全。具体操作，请参见[吊销集群的KubeConfig凭证](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/吊销集群的KubeConfig凭证.md)。

**相关文档**  


[获取集群kubeconfig接口](/cn.zh-CN/API参考/集群/获取集群kubeconfig接口.md)

