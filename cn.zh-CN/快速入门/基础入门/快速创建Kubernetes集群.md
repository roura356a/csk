# 快速创建Kubernetes集群 {#task_mmv_33q_n2b .task}

您需要开通容器服务、资源编排（ROS）服务和访问控制（RAM）服务。更多限制和使用说明的信息，参见[创建Kubernetes集群](../../../../cn.zh-CN/用户指南/Kubernetes 集群/集群管理/创建Kubernetes集群.md#)。

登录 [容器服务管理控制台](https://cs.console.aliyun.com/)、[ROS 管理控制台](https://ros.console.aliyun.com/) 和 [RAM 管理控制台](https://ram.console.aliyun.com/) 开通相应的服务。

本例将演示如何快速创建一个Kubernetes集群，部分配置采用默认或最简配置。

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com)。 
2.  在 Kubernetes 菜单下，单击左侧导航栏的**集群**，进入集群列表页面，单击页面右上角的创建**Kubernetes 集群**。 
3.  配置集群参数。 

    本例中大多数配置保留默认值，具体的配置如下图所示：

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16128/15325969287352_zh-CN.png)

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16128/15325969287353_zh-CN.png)

    |配置项|配置说明|
    |---|----|
    |集群名称|名称为1-63个字符，可包含数字、汉字、英文字符，或"-"。|
    |地域和可用区|集群所处地域和可用区|
    |专有网络|可选择**自动创建**或**使用已有**。    -   选择**自动创建**，创建集群时，系统会自动为您的 VPC 创建一个 NAT 网关。
    -   选择**使用已有**，如果您使用的 VPC 中当前已有 NAT 网关，容器服务会使用已有的 NAT 网关；如果 VPC 中没有 NAT 网关，系统会默认自动为您创建一个 NAT 网关。如果您不希望系统自动创建 NAT 网关，可以取消勾选页面下方的**为专有网络配置 SNAT**。

**说明：** 若选择不自动创建 NAT 网关，您需要自行配置 NAT 网关实现 VPC 安全访问公网环境，或者手动配置 SNAT，否则 VPC 内实例将不能正常访问公网，会导致集群创建失败。

|
    |节点类型|支持按量付费和包年包月|
    |Master节点配置|选择实例规格和系统盘：    -   **实例规格**：参见[实例规格族](../../../../cn.zh-CN/产品简介/实例规格族.md#)
    -   **系统盘**：支持SSD云盘和高效云盘。
|
    |Worker节点配置|您可选择新增实例或添加已有实例。若选择新增实例，可进行如下配置。    -   **实例规格**：参见[实例规格族](../../../../cn.zh-CN/产品简介/实例规格族.md#)
    -   **系统盘**：支持SSD云盘和高效云盘。
    -   **挂载数据盘**:支持SSD云盘、高效云盘和普通云盘。
|
    |登录方式|支持设置密钥和密码，关于使用密钥登录的信息，参见[SSH密钥对访问Kubernetes集群](../../../../cn.zh-CN/用户指南/Kubernetes 集群/集群管理/SSH密钥对访问Kubernetes集群.md#)|
    |Pod网络CIDR和Service CIDR（可选项）|具体如何规划可参见[VPC下 Kubernetes 的网络地址段规划](../../../../cn.zh-CN/用户指南/Kubernetes 集群/VPC下 Kubernetes 的网络地址段规划.md#)。**说明：** 选择**使用已有**专有网络时可配置

|
    |配置SNAT|选择**自动创建**专有网络时必选；选择**使用已有**专有网络，可选，若不选择，需要自行配置NAT网关，或手动配置SNAT。|
    |SSH登录|     -   选择开放公网 SSH 登录，您可以 SSH 访问集群。
    -   选择不开放公网 SSH 登录，将无法通过 SSH 访问集群，也无法通过 kubectl 连接 集群。您可手动进行配置，具体操作参见[SSH访问Kubernetes集群](../../../../cn.zh-CN/用户指南/Kubernetes 集群/集群管理/SSH访问Kubernetes集群.md#) 。
 |
    |云监控插件|在节点上安装云监控插件，可以在云监控控制台查看所创建ECS实例的监控信息。|
    |RDS白名单（可选项）|将节点 IP 添加到 RDS 实例的白名单。**说明：** 选择**使用已有**专有网络时可配置

|
    |高级选项|     -   网络插件：支持Flannel和Terway，默认启用Flannel。
    -   节点Pod数量：单节点可运行 Pod 数量的上限
    -   自定义镜像：设置是否安装自定义镜像。否则会使用默认CentOS镜像。
    -   集群CA：设置是否开启集群CA。
 |

4.  单击右上角**创建集群**，启动部署。 

集群创建成功后，您可以在 Kubernetes 集群列表页面查看所创建的集群。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16128/15325969287356_zh-CN.png)

至此，您已经快速创建一个Kubernetes集群。

