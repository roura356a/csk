# 通过SSH访问Kubernetes集群

如果您在创建集群时，选择不开放公网SSH访问，您将无法SSH访问Kubernetes集群，而且也无法通过kubectl连接Kubernetes集群。如果创建集群后，您需要SSH访问您的集群，可以手动为ECS实例绑定EIP，并配置安全组规则，开放SSH（22）端口。

有关在创建集群时如何开启SSH访问的具体步骤，请参见[创建集群时绑定EIP](/intl.zh-CN/Kubernetes集群用户指南/集群管理/连接集群/通过公网访问集群API Server.md)。

## 操作步骤

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在**集群信息**页面，单击集群资源页签，找到您的**APIServer 负载均衡（SLB）**，并单击实例ID链接。

5.  在左侧导航栏，选择**实例** \> **实例管理**，单击**监听**页签，然后单击**添加监听**。

6.  添加SSH监听规则。添加SSH监听规则。请参见[添加TCP监听](/intl.zh-CN/传统型负载均衡CLB/用户指南/监听/添加TCP监听.md)。

    监听创建完成后，您就可以使用该负载均衡的服务地址SSH访问您的集群了。

    ![SSH访问新集群](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1475659951/p9054.png)


