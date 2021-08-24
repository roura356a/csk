---
keyword: [吊销集群Kubeconfig凭证, k8s集群]
---

# 吊销集群的KubeConfig凭证

在常见的多租场景下，容器服务会为不同角色的用户签发带有其身份信息的KubeConfig凭证用于连接集群。当企业内部员工离职或是某签发KubeConfig遭到疑似泄露等情况发生时，吊销该集群的KubeConfig可有效保障集群的安全。本文主要介绍在容器服务中如何吊销已经分发的用户KubeConfig凭证。

-   如果您需要吊销的是Serverless Kubernetes集群的KubeConfig凭证，请选择2019年9月6号以后创建的集群。
-   如果您需要吊销的是专有版或者托管版Kubernetes集群的KubeConfig凭证：
    -   当前用户是主账号，则2019年10月15日之前创建的Kubernetes集群无法吊销主账号KubeConfig凭证。
    -   当前用户是子账号，则可以对管理范围内的Kuberneres集群吊销KubeConfig凭证。

吊销KubeConfig凭证包含以下两种场景：

-   当前用户可以吊销自身的KubeConfig凭证。
-   主账号可以吊销其子账号的KubeConfig凭证。

## 吊销自身KubeConfig凭证

**说明：** 吊销KubeConfig后，用户就无法使用原KuberConfig连接集群，请谨慎操作。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

    **说明：**

    如果您使用主账号登录，请选择2019年10月15日及以后创建的集群。

    如果您需要吊销的是Serverless Kubernetes集群的KubeConfig凭证，请选择2019年9月6号以后创建的集群。

4.  在**集群信息**页面，单击**连接信息**页签，单击**吊销 KubeConfig**。

    ![kubeconfig凭证](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/1167858261/p66613.png)

5.  在弹出的对话框中单击**确定**。

    吊销当前账号下该集群的KubeConfig凭证。此时，系统会自动为您分配新的KubeConfig凭证。


## 主账号吊销其子账号的KubeConfig凭证

1.  使用主账号登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**授权管理**。

3.  在选择子账号配置项页面的**子账号**页签，在需要吊销的子账号右侧单击**吊销KubeConfig**。

    此时会弹出该子账号名下所有的集群ID。

    ![注销子账号凭证](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/2195659951/p66623.png)

4.  找到需要吊销的集群所对应的集群ID，单击**吊销KubeConfig**。

5.  在弹出的对话框中单击**确定**。


