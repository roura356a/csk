# SSH访问Kubernetes集群 {#concept_qy2_f2g_vdb .concept}

如果您在创建集群时，选择不开放公网 SSH 访问，您将无法 SSH 访问 Kubernetes 集群，而且也无法通过 kubectl 连接 Kubernetes 集群。如果创建集群后，您需要 SSH 访问您的集群，可以手动为 ECS 实例绑定 EIP，并配置安全组规则，开放 SSH（22）端口。

## 操作步骤 {#section_f3q_grx_b2b .section}

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**集群**，进入 Kubernetes 集群列表页面。
3.  选择所需的集群并单击右侧的**管理**。
4.  在**集群资源**中，找到您的公网负载均衡实例，并单击实例 ID，页面跳转至您的公网负载均衡实例的详细信息页面。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6882/15331795691993_zh-CN.png)

5.  单击左侧导航栏中的**监听** 并单击页面右上角的**添加监听**。
6.  添加 SSH 监听规则。

    1.  **前端协议（端口）**选择 TCP：22。
    2.  **后端协议（端口）**选择 TCP：22。
    3.  选择 **使用服务器组** 并选择**虚拟服务器组**。
    4.  **服务器组ID** 选择**sshVirtualGroup**。
    5.  单击**下一步** 并单击**确认**，创建监听。
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6882/15331795691990_zh-CN.png)

7.  监听创建完成后，您就可以使用该负载均衡的服务地址 SSH 访问您的集群了。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6882/15331795691991_zh-CN.png)


