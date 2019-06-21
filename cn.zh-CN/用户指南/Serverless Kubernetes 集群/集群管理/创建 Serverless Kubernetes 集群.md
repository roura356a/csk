# 创建 Serverless Kubernetes 集群 {#task_e3c_311_ydb .task}

您可以通过容器服务管理控制台非常方便地快速创建 Serverless Kubernetes 集群。

登录 [容器服务管理控制台](https://cs.console.aliyun.com/) 和 [RAM 管理控制台](https://ram.console.aliyun.com/) 开通相应的服务。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在Kubernetes 菜单下，单击左侧导航栏的**集群** \> **集群**，进入集群列表页面。
3.  单击页面右上角的**创建 Kubernetes 集群**，在弹出的选择集群模板中，选择**标准 Serverless 集群**页面，单击**创建**。![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16481/156108822749845_zh-CN.png)

 

    默认进入 **Serverless Kubernetes** 集群配置页面。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16481/156108822849846_zh-CN.png)

4.  填写集群的名称。 

    集群名称应包含 1-63 个字符，可包含数字、汉字、英文字符或连字符（-）。

5.  选择集群所在的资源组。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16481/156108822849848_zh-CN.png)

6.  选择集群所在的地域和可用区。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16481/156108822849849_zh-CN.png)

7.  设置集群的网络。 

    Kubernetes 集群仅支持专有网络。支持**自动创建**和**使用已有**的 VPC。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16481/156108822849850_zh-CN.png)

    **说明：** 

    -   **自动创建**：集群会自动新建一个VPC，并在VPC中自动创建NAT网关以及配置SNAT规则。
    -   **使用已有**：您可以在已有 VPC 列表中选择所需的 VPC 和交换机。如需访问公网，比如下载容器镜像，要配置NAT网关，建议将容器镜像上传到集群所在区域的阿里云镜像服务，并通过内网VPC地址拉取镜像。
8.  设置是否为专有网络创建 NAT网关 并配置 SNAT 规则。 

    **说明：** 若您选择**自动创建** VPC 时必须配置 SNAT；若您选择**使用已有**VPC，可选择是否自动配置SNAT网关。若选择不自动配置 SNAT，您可自行配置NAT 网关实现 VPC 安全访问公网环境；或者手动配置 SNAT，否则 VPC 内实例将不能正常访问公网，会导致集群创建失败。

9.  勾选是否开启基于PrivateZone的服务发现功能，该功能允许您在集群VPC内通过服务域名访问服务。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16481/156108822849851_zh-CN.png)

    **说明：** 在使用勾选该功能前，请确认您已开通PrivateZone服务，参见[基于云解析 PrivateZone 的服务发现](intl.zh-CN/用户指南/Serverless Kubernetes 集群/基于云解析 PrivateZone 的服务发现.md#)。

10. 为集群绑定标签。 输入键和对应的值，单击**添加**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16481/156108822849852_zh-CN.png)

    **说明：** 

    -   键是必需的，而 值 是可选的，可以不填写。
    -   键 不能是 aliyun、http:// 、https:// 开头的字符串，不区分大小写，最多 64 个字符。
    -   值 不能是 http:// 或 https://，可以为空，不区分大小写，最多 128 个字符。
    -   同一个资源，标签键不能重复，相同标签键（Key）的标签会被覆盖。
    -   如果一个资源已经绑定了 20 个标签，已有标签和新建标签会失效，您需要解绑部分标签后才能再绑定新的标签。
11. 勾选**无服务器 Kubernetes 服务协议**。
12. 在页面右侧，单击**创建集群**，启动部署。

集群创建成功后，您可以在容器服务管理控制台的 Kubernetes 集群列表页面查看所创建的 Serverless 集群。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16481/156108822810238_zh-CN.png)

您可以单击右侧的**管理**，查看集群的基本信息和连接信息。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16481/156108822810239_zh-CN.png)

