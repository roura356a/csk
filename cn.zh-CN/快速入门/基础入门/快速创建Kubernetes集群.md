# 快速创建Kubernetes集群 {#task_mmv_33q_n2b .task}

您需要开通容器服务、资源编排（ROS）服务和访问控制（RAM）服务。更多限制和使用说明的信息，参见[创建Kubernetes集群](../../../../intl.zh-CN/用户指南/Kubernetes 集群/集群管理/创建Kubernetes集群.md#)。

登录 [容器服务管理控制台](https://cs.console.aliyun.com/)、[ROS 管理控制台](https://ros.console.aliyun.com/) 和 [RAM 管理控制台](https://ram.console.aliyun.com/) 开通相应的服务。

本例将演示如何快速创建一个Kubernetes集群，部分配置采用默认或最简配置。

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏的**集群** \> **集群**，进入集群列表页面。
3.  单击页面右上角的**创建Kubernetes 集群**，在弹出的选择集群模板中，选择**标准专有集群**页面，单击**创建**。
4.  配置集群参数。 

    本例中大多数配置保留默认值，具体的配置如下图所示。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16128/156275762451229_zh-CN.png)

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16128/156275762551230_zh-CN.png)

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16128/156275762551231_zh-CN.png)

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16128/156275762551232_zh-CN.png)

    |配置项|配置说明|
    |---|----|
    |集群名称|名称为1-63个字符，可包含数字、汉字、英文字符，或"-"。|
    |资源组|集群所处的资源组。|
    |地域|集群所处地域。|
    |专有网络|集群的专有网络。 您可以在已有 VPC 列表中选择所需的 VPC 。

    -   如果您使用的 VPC 中当前已有 NAT 网关，容器服务会使用已有的 NAT 网关。
    -   如果 VPC 中没有 NAT 网关，系统会默认自动为您创建一个 NAT 网关。如果您不希望系统自动创建 NAT 网关，可以取消勾选页面下方的**为专有网络配置 SNAT**。

**说明：** 若选择不自动创建 NAT 网关，您需要自行配置 NAT 网关实现 VPC 安全访问公网环境，或者手动配置 SNAT，否则 VPC 内实例将不能正常访问公网，会导致集群创建失败。

 |
    |虚拟交换机|集群的虚拟交换机。您可以在已有 vswitch 列表中选择所需的 vswitch 。 您可以根据需要，选择1~3个 vswitch。推荐您选择3个 vswitch。

 |
    |节点类型|支持按量付费和包年包月。|
    |购买时长和自动续费（可选项）|您可以选择购买的时长及自动续费的时长。 **说明：** 选择**包年包月**节点类型时可配置。

 |
    |Master节点配置|选择实例数量、实例规格和系统盘。     -   **Master 实例数量**：选择3个或5个实例
    -   **实例规格**：参见[实例规格族](../../../../intl.zh-CN/实例/实例规格族.md#)
    -   **系统盘**：支持SSD云盘和高效云盘
 |
    |Worker节点配置|您可选择新增实例或添加已有实例。若选择新增实例，可进行如下配置。     -   **实例规格**：支持选择配置1~10个实例规格。参见[实例规格族](../../../../intl.zh-CN/实例/实例规格族.md#)。
    -   **系统盘**：支持SSD云盘和高效云盘。
    -   **挂载数据盘**：支持SSD云盘、高效云盘和普通云盘。
 |
    |Docker 版本和Kubernetes 版本（可选项）|显示当前支持的Docker版本和Kubernetes版本，您可查看对应版本，并根据需要进行选择。 **说明：** 选择添加已有 Worker 实例时可配置。

 |
    |登录方式|支持设置密钥和密码，关于使用密钥登录的信息，参见[SSH密钥对访问Kubernetes集群](../../../../intl.zh-CN/用户指南/Kubernetes 集群/集群管理/SSH密钥对访问Kubernetes集群.md#)。|
    |网络插件|支持Flannel和Terway，默认启用Flannel。具体可参考[如何选择Kubernetes集群网络插件：Terway和Flannel](../../../../intl.zh-CN/常见问题/如何选择Kubernetes集群网络插件：Terway和Flannel.md#)。|
    |Pod网络CIDR和Service CIDR|具体如何规划可参见[VPC下 Kubernetes 的网络地址段规划](../../../../intl.zh-CN/用户指南/Kubernetes 集群/VPC下 Kubernetes 的网络地址段规划.md#)。|
    |配置SNAT|可选，若不选择，需要自行配置NAT网关，或手动配置SNAT。|
    |公网访问|     -   如果选择开放，会创建一个EIP，并挂载到内网SLB上。此时，Master节点的6443端口（对应API Server）暴露出来，用户可以在外网通过kubeconfig连接/操作集群。
    -   若选择不开放，不会创建EIP，用户只能在VPC内部用kubeconfig连接/操作集群。
 |
    |SSH登录（可选项）|     -   选择开放公网 SSH 登录，您可以 SSH 访问集群。
    -   选择不开放公网 SSH 登录，将无法通过 SSH 访问集群，也无法通过 kubectl 连接 集群。您可手动进行配置，具体操作参见[SSH访问Kubernetes集群](../../../../intl.zh-CN/用户指南/Kubernetes 集群/集群管理/SSH访问Kubernetes集群.md#) 。
 **说明：** 勾选**公网访问**时可配置。

 |
    |云监控插件|在节点上安装云监控插件，可以在云监控控制台查看所创建ECS实例的监控信息。|
    |日志服务|您可使用已有Project或新建一个Project。 勾选**使用日志服务**，会在集群中自动配置日志服务插件。创建应用时，您可通过简单配置，快速使用日志服务，详情参见[使用日志服务进行Kubernetes日志采集](../../../../intl.zh-CN/用户指南/Kubernetes集群/日志管理/使用日志服务进行Kubernetes日志采集.md#)。

 |
    |RDS白名单|将节点 IP 添加到 RDS 实例的白名单。|
    |实例保护|为防止通过控制台或 API 误删除释放集群节点，该选项默认勾选。|
    |标签|为集群绑定标签。 **说明：** 

    -   键是必需的，而 值 是可选的，可以不填写。
    -   键 不能是 aliyun、http:// 、https:// 开头的字符串，不区分大小写，最多 64 个字符。
    -   值 不能是 http:// 或 https://，可以为空，不区分大小写，最多 128 个字符。
    -   同一个资源，标签键不能重复，相同标签键（Key）的标签会被覆盖。
    -   如果一个资源已经绑定了 20 个标签，已有标签和新建标签会失效，您需要解绑部分标签后才能再绑定新的标签。
 |
    |高级选项|     -   节点Pod数量：单节点可运行 Pod 数量的上限。
    -   kube-proxy代理模式：支持 iptables 和 IPVS 两种模式。
        -   iptables：成熟稳定的kube-proxy代理模式，Kubernetes service的服务发现和负载均衡使用iptables规则配置，但性能一般，受规模影响较大，适用于集群存在少量的service。
        -   IPVS：高性能的kube-proxy代理模式，Kubernetes service的服务发现和负载均衡使用Linux ipvs模块进行配置，适用于集群存在大量的service，对负载均衡有高性能要求的场景。
    -   CPU Policy：设置CPU Policy。
        -   none：默认策略，表示启用现有的默认 CPU 亲和方案。
        -   static：允许为节点上具有某些资源特征的 Pod 赋予增强的 CPU 亲和性和独占性。
    -   设置节点服务端口范围。默认端口范围为30000~32767。
    -   集群 CA：设置是否开启集群CA。

如果勾选自定义集群 CA，可以将 CA 证书添加到 Kubernetes 集群中，加强服务端和客户端之间信息交互的安全性。

    -   工作流引擎：设置是否使用AGS。
        -   如果勾选 AGS，则创建集群时系统自动安装 AGS 工作流插件。
        -   如果不勾选，则需要手动安装 AGS 工作流插件，请参见[AGS命令行帮助](../../../../intl.zh-CN/用户指南/Kubernetes集群/工作流/AGS命令行帮助.md#)。
 |

5.  单击**创建集群**，启动部署。 集群创建成功后，您可以在容器服务管理控制台的 Kubernetes 集群列表页面查看所创建的集群。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16128/15627576257356_zh-CN.png)


