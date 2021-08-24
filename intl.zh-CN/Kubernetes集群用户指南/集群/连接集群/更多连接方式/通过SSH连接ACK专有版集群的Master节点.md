---
keyword: [ACK专有版, SSH连接, Master节点]
---

# 通过SSH连接ACK专有版集群的Master节点

由于ACK专有版集群的Master节点由您自行创建并维护，因此为了方便您的维护管理，容器服务ACK支持通过SSH方式连接专有版集群的Master节点。本文介绍如何为专有版集群开通SSH公网登录，以及如何通过SSH方式连接专有版集群的Master节点。

ACK专有版集群的API Server已绑定EIP。如未绑定，请参见[控制集群API Server的公网访问能力](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/集群访问控制/控制集群API Server的公网访问能力.md)。

-   创建专有版集群时，如果您选中了**开放公网SSH登录**，ACK会自动为您生成Master节点SSH 连接地址，在集群的**基本信息**页签下可以查看。此时您可以直接使用该地址登录专有版集群的Master节点。

    ![master SSH](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/8525328261/p302024.png)

-   如果创建时没有开放公网SSH登录，您需要为API Server的SLB手动添加SSH（22）端口监听，之后才可以通过SSH方式登录Master节点。

## 操作步骤

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在**集群信息**页面，单击集群资源页签，找到您的**APIServer 负载均衡（SLB）**，并单击右侧实例ID链接。

5.  在负载均衡SLB左侧导航栏，选择**传统型负载均衡CLB（原SLB）** \> **实例管理**，单击**监听**页签，然后单击**添加监听**。

    以下为简要配置步骤，更详细的操作，请参见[添加TCP监听](/intl.zh-CN/传统型负载均衡CLB/CLB用户指南/监听/添加TCP监听.md)。

    1.  选择负载均衡协议为**TCP**，设置**监听端口**为**22**（即SSH所用端口），单击**下一步**。

    2.  选择**默认服务器组**，在**已添加服务器**区域为所有的Master节点配置端口为**22**，根据负载均衡的需求设置不同节点的权重，然后单击**下一步**。

    3.  在**健康检查**页面，单击**下一步**。

    4.  在**配置审核**页面，单击**提交**。

    添加成功后，会在下面的**监听**页签中显示新创建的名为**tcp\_22**的监听。

    ![ssh-listener](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/1339328261/p302066.png)

6.  在SLB实例的**实例详情**页签，查看SLB的弹性（即公网）地址。

    该地址便成为专有版集群对外的Master节点SSH地址。

    ![SSH访问新集群](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/1339328261/p9054.png)

7.  通过SSH登录Master节点SSH地址。

    在创建集群时，有两种登录方式可供选择：设置密钥和设置密码（详情请参见[创建Kubernetes专有版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes专有版集群.md)）。请根据您的实际设置选择以下登录方式：

    -   密码登录：用户名默认为root，密码为您创建集群时设置的密码。
    -   密钥登录：请参见[通过密钥认证登录Linux实例](/intl.zh-CN/实例/连接实例/使用第三方客户端工具连接实例/使用SSH密钥对连接Linux实例.md)。

