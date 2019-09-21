# SSH访问Kubernetes集群 {#task_1664343 .task}

如果您在创建集群时，选择不开放公网 SSH 访问，您将无法 SSH 访问 Kubernetes 集群，而且也无法通过 kubectl 连接 Kubernetes 集群。如果创建集群后，您需要 SSH 访问您的集群，可以手动为 ECS 实例绑定 EIP，并配置安全组规则，开放 SSH（22）端口。

由于创建集群所使用的 ROS 编排模板的版本不同，不同时间点创建的集群开启 SSH 访问的操作会略有不同，请按照您的集群的创建时间选择不同的操作方法。

## 2017 年 12 月之前创建的集群 {#section_gp4_x8p_6ys .section}

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**集群**，进入 Kubernetes 集群列表页面。
3.  选择所需的集群并查看集群 ID。
4.  登录 [ROS 管理控制台](https://ros.console.aliyun.com)。
5.  选择您的集群所在的地域，在搜索框中输入k8s-for-cs-clisterid搜索您的集群对应的资源栈，并单击右侧的**管理**。 

    ![旧集群管理](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16642/15690625259052_zh-CN.png)

6.  单击左侧导航栏中的**资源**，在资源列表中找到名为 k8s\_master\_slb\_internet的资源（该资源为您的集群的公网负载均衡实例）并单击资源 ID，页面跳转至您的公网负载均衡实例的详细信息页面。
7.  单击左侧导航栏中的**实例** \> **实例管理**，单击**添加监听**。
8.  添加 SSH 监听规则。请参见[添加TCP监听](../../../../../intl.zh-CN/监听/添加TCP监听.md#)。
9.  监听创建完成后，您就可以使用该负载均衡的服务地址 SSH 访问您的集群了。 

    ![SSH访问旧集群](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16642/15690625259054_zh-CN.png)


## 2017 年 12 月之后创建的集群 {#section_x2p_i2f_dq7 .section}

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**集群**，进入 Kubernetes 集群列表页面。
3.  选择所需的集群并单击右侧的**管理**。
4.  在**集群资源**中，找到您的公网负载均衡实例，并单击实例 ID，页面跳转至您的公网负载均衡实例的详细信息页面。 

    ![新集群资源](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16642/15690625259055_zh-CN.png)

5.  单击左侧导航栏中的**实例** \> **实例管理**，单击**添加监听**。
6.  添加 SSH 监听规则。添加 SSH 监听规则。请参见[添加TCP监听](../../../../../intl.zh-CN/监听/添加TCP监听.md#)。
7.  监听创建完成后，您就可以使用该负载均衡的服务地址 SSH 访问您的集群了。 

    ![SSH访问新集群](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16642/15690625259054_zh-CN.png)


## 操作步骤 {#section_owe_loq_l79 .section}

1.  [容器服务管理控制台](https://cs.console.aliyun.com)
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**集群**，进入 Kubernetes 集群列表页面。
3.  选择所需的集群并单击右侧的**管理**。
4.  在**集群资源**中，找到您的公网负载均衡实例，并单击实例 ID，页面跳转至您的公网负载均衡实例的详细信息页面。 

    ![集群资源](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16642/15690625259055_zh-CN.png)

5.  单击左侧导航栏中的**实例** \> **实例管理**，单击**添加监听**。
6.  添加 SSH 监听规则。添加 SSH 监听规则。请参见[添加TCP监听](../../../../../intl.zh-CN/监听/添加TCP监听.md#)。
7.  监听创建完成后，您就可以使用该负载均衡的服务地址 SSH 访问您的集群了。 

    ![SSH访问集群](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16642/15690625259054_zh-CN.png)


