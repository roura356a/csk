# 创建 Serverless Kubernetes 集群 {#task_e3c_311_ydb .task}

您可以通过容器服务管理控制台非常方便地快速创建 Serverless Kubernetes 集群。

登录 [容器服务管理控制台](https://cs.console.aliyun.com/) 和 [RAM 管理控制台](https://ram.console.aliyun.com/) 开通相应的服务。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。 
2.  在Kubernetes 菜单下，单击左侧导航栏的**集群**，进入集群列表页面。 
3.  单击页面右上角的**创建 Serverless Kubernetes 集群**。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16481/153510529610234_zh-CN.png)

4.  填写集群的名称。 

    集群名称应包含 1-63 个字符，可包含数字、汉字、英文字符或连字符（-）。

5.  选择集群所在的地域和可用区。 

    **说明：** Serverless Kubernetes 集群目前处于公测阶段，仅支持华东 2 （上海）地域。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16481/153510529710235_zh-CN.png)

6.  设置集群的网络。 

    -   **网络类型**：Kubernetes 集群仅支持专有网络。
    -   **专有网络**：支持**自动创建**和**使用已有**的 VPC。

        **说明：** 

        -   **自动创建**：集群会自动新建一个VPC，并在VPC中自动创建NAT网关以及配置SNAT规则。
        -   **使用已有**：您可以在已有 VPC 列表中选择所需的 VPC 和交换机。如需访问公网，比如下载容器镜像，要配置NAT网关，建议将容器镜像上传到集群所在区域的阿里云镜像服务，并通过内网VPC地址拉取镜像。
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16481/153510529710236_zh-CN.png)

7.  勾选是否开启基于PrivateZone的服务发现功能，该功能允许您在集群VPC内通过服务域名访问服务。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16481/153510529710237_zh-CN.png)

    **说明：** 在使用勾选该功能前，请确认您已开通PrivateZone服务，参见[基于云解析 PrivateZone 的服务发现](intl.zh-CN/用户指南/Serverless Kubernetes 集群/基于云解析 PrivateZone 的服务发现.md#)。

8.  勾选**无服务器 Kubernetes 服务协议**。 
9.  在页面右侧，单击**创建集群**，启动部署。 

集群创建成功后，您可以在容器服务管理控制台的 Kubernetes 集群列表页面查看所创建的 Serverless 集群。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16481/153510529710238_zh-CN.png)

您可以单击右侧的**管理**，查看集群的基本信息和连接信息。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16481/153510529710239_zh-CN.png)

