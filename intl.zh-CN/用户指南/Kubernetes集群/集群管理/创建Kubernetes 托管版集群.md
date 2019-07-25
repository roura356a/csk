# 创建Kubernetes 托管版集群 {#task_skz_qwk_qfb .task}

您可以通过容器服务控制台非常方便的创建Kubernetes托管版集群。

您需要开通容器服务、资源编排（ROS）服务、弹性伸缩（ESS）服务和访问控制（RAM）服务。

登录 [容器服务管理控制台](https://cs.console.aliyun.com/)、[ROS 管理控制台](https://ros.console.aliyun.com/) 、 [RAM 管理控制台](https://ram.console.aliyun.com/) 和 [弹性伸缩控制台](https://essnew.console.aliyun.com) 开通相应的服务。

**说明：** 容器服务 Kubernetes 集群部署依赖阿里云资源编排 ROS 的应用部署能力，所以创建 Kubernetes 托管版集群前，您需要开通 ROS。

-   随集群一同创建的负载均衡实例只支持按量付费的方式。
-   Kubernetes 集群仅支持专有网络 VPC。
-   每个账号默认可以创建的云资源有一定的配额，如果超过配额创建进群会失败。请在创建集群前确认您的配额。如果您需要提高您的配额，请提交工单申请。
    -   每个账号默认最多可以创建 5 个集群（所有地域下），每个集群中最多可以添加 40 个节点。如果您需要创建更多的集群或者节点，请提交工单申请。

        **说明：** Kubernetes集群中，VPC默认路由条目不超过48条，意味着Kubernetes集群使用VPC时，默认节点上限是48个，如果需要更大的节点数，需要您先对目标VPC开工单，提高VPC路由条目，再对容器服务提交工单。

    -   每个账号默认最多可以创建 100 个安全组。
    -   每个账号默认最多可以创建 60 个按量付费的负载均衡实例。
    -   每个账号默认最多可以创建 20 个EIP。

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下， 单击左侧导航栏中的**集群** \> **集群**，单击页面右上角的**创建 Kubernetes 集群**。
3.  在弹出的选择集群模板页面，选择标准托管版集群页面，并单击**创建**，进入**Kubernetes 托管版**页面。 

    ![集群配置页面](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156404746352788_zh-CN.png)

4.  选择集群所在的资源组。 

    ![资源组](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156404746349901_zh-CN.png)

5.  填写集群的名称。 

    集群名称应包含 1~63 个字符，可包含数字、汉字、英文字符或连字符（-）。

    ![集群名称](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156404746321257_zh-CN.png)

6.  选择集群所在的地域。 

    ![地域](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156404746349903_zh-CN.png)

7.  设置集群的网络。Kubernetes 托管版集群仅支持专有网络。 

    您可以在已有 VPC 列表中选择所需的 VPC 。

    ![专有网络](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15640474639037_zh-CN.png)

    -   如果您使用的 VPC 中当前已有 NAT 网关，容器服务会使用已有的 NAT 网关。
    -   如果 VPC 中没有 NAT 网关，系统会默认自动为您创建一个 NAT 网关。如果您不希望系统自动创建 NAT 网关，可以取消勾选页面下方的**为专有网络配置 SNAT**。

        **说明：** 若选择不自动创建 NAT 网关，您需要自行配置 NAT 网关实现 VPC 安全访问公网环境，或者手动配置 SNAT，否则 VPC 内实例将不能正常访问公网，会导致集群创建失败。

8.  设置虚拟交换机。 

    您可以在已有 vswitch 列表中，根据**可用区**选择1~3个交换机 。如果没有您需要的交换机，可以通过单击**创建虚拟交换机**进行创建，请参见[管理交换机](../../../../../intl.zh-CN/用户指南/专有网络和子网/管理交换机.md#)。

    ![虚拟交换机](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156404746349904_zh-CN.png)

9.  设置 Worker 节点的配置信息。您可选择新增实例或添加已有实例。 

    -   若您选择新增实例，则需要进行如下配置。

        ![worker实例配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15640474649039_zh-CN.png)

        -   **节点类型**：支持按量付费和包年包月两种节点类型。
        -   **实例规格**：参见[实例规格族](../../../../../intl.zh-CN/实例/实例规格族.md#)。
        -   **已选规格**：实例规格选中的规格。
        -   **数量**：新增 Worker 实例的数量。
        -   **系统盘**：支持SSD云盘和高效云盘。
        -   **挂载数据盘**：支持SSD云盘、高效云盘和普通云盘。
    -   若您选择添加已有实例，则需要预先在此地域下创建 ECS 云服务器。

        ![worker实例](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15640474649040_zh-CN.png)

    **说明：** 

    -   每个集群最少包含 2 个节点。
    -   每个集群最多可包含 48 个节点。如果您需要创建更多的节点，请提交工单申请。
10. 显示当前支持的Docker版本、Kubernetes版本和操作类型系统，您可查看对应版本，并根据需要进行选择。本文以Linux为例进行介绍，关于Windows的操作请参见[创建Windows Kubernetes 集群](intl.zh-CN/用户指南/Kubernetes集群/集群管理/创建Windows Kubernetes 集群.md#)。![版本](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156404746441590_zh-CN.png)


11. 设置登录方式。 

    ![登录方式](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156404746421262_zh-CN.png)

    -   设置密钥。

        您需要在创建集群的时候选择密钥对登录方式，单击**新建密钥对**，跳转到ECS云服务器控制台，创建密钥对，参见[创建 SSH 密钥对](../../../../../intl.zh-CN/安全/SSH密钥对/使用SSH密钥对.md#)。密钥对创建完毕后，设置该密钥对作为登录集群的凭据。

    -   设置密码。
        -   **登录密码**：设置节点的登录密码。
        -   **确认密码**：确认设置的节点登录密码。
12. 设置启用的网络插件，支持Flannel和Terway网络插件，具体可参考[如何选择Kubernetes集群网络插件：Terway和Flannel](../intl.zh-CN/常见问题/如何选择Kubernetes集群网络插件：Terway和Flannel.md#)。 

    ![网络插件](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156404746421269_zh-CN.png)

    -   Flannel：简单稳定的社区的Flannel 插件。但功能偏简单，支持的特性少，例如：不支持基于Kubernetes标准的Network Policy。
    -   Terway：阿里云容器服务自研的网络插件，支持将阿里云的弹性网卡分配给容器，支持Kubernetes的Network Policy来定义容器间的访问策略，支持对单个容器做带宽的限流。
13. 设置**Pod 网络 CIDR** 和**Service CIDR**。 

    ![网络地址](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156404746421265_zh-CN.png)

    **说明：** **Pod 网络 CIDR**和**Service CIDR**两者都不能与 VPC 及 VPC 内已有 Kubernetes 集群使用的网段重复，创建成功后不能修改。且 Service 地址段也不能和 Pod 地址段重复，有关 Kubernetes 网络地址段规划的信息，请参考[VPC下 Kubernetes 的网络地址段规划](intl.zh-CN/最佳实践/集群/VPC下 Kubernetes 的网络地址段规划.md#)。

14. 设置是否为专有网络配置 SNAT 网关。 

    ![配置SNAT](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156404746421267_zh-CN.png)

    **说明：** 若不勾选**为专有网络配置 SNAT**，您需要自行配置NAT 网关实现 VPC 安全访问公网环境；或者手动配置 SNAT，否则 VPC 内实例将不能正常访问公网，会导致集群创建失败。

15. 设置是否启用公网访问。 

    API Server提供了各类资源对象（Pod，Service等）的增删改查及watch等HTTP Rest接口。

    ![公网访问](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156404746449921_zh-CN.png)

    -   如果选择开放，会创建一个 EIP，并挂载到内网 SLB 上。此时，Master 节点的6443端口（对应API Server）暴露出来，用户可以在外网通过kubeconfig 连接/操作集群。
    -   如果选择不开放，则不会创建 EIP，用户只能在 VPC 内部用 kubeconfig 连接/操作集群。
16. 设置是否启用云监控插件。 您可以选择在 ECS 节点上安装云监控插件，从而在云监控控制台查看所创建 ECS 实例的监控信息。

    ![云监控插件](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156404746521268_zh-CN.png)

17. 设置是否启用日志服务，您可使用已有Project或新建一个Project。 

    勾选**使用日志服务**，会在集群中自动配置日志服务插件。创建应用时，您可通过简单配置，快速使用日志服务，详情参见[使用日志服务进行Kubernetes日志采集](intl.zh-CN/用户指南/Kubernetes集群/日志管理/使用日志服务进行Kubernetes日志采集.md#)。

    ![日志服务](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156404746549924_zh-CN.png)

18. 设置是否安装 Ingress 组件。 默认勾选**安装 Ingress 组件**，请参见[Ingress 支持](intl.zh-CN/用户指南/Kubernetes集群/网络管理/Ingress 支持.md#)。

    ![ingress](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/156404746552875_zh-CN.png)

19. 设置RDS白名单。 

    将节点 IP 添加到 RDS 实例的白名单。

    ![RDS白名单](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156404746521270_zh-CN.png)

20. 为集群绑定标签。 

    输入键和对应的值，单击**添加**。

    ![标签](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156404746549925_zh-CN.png)

    **说明：** 

    -   键是必需的，而 值 是可选的，可以不填写。
    -   键 不能是 aliyun、http:// 、https:// 开头的字符串，不区分大小写，最多 64 个字符。
    -   值 不能是 http:// 或 https://，可以为空，不区分大小写，最多 128 个字符。
    -   同一个资源，标签键不能重复，相同标签键（Key）的标签会被覆盖。
    -   如果一个资源已经绑定了 20 个标签，已有标签和新建标签会失效，您需要解绑部分标签后才能再绑定新的标签。
21. 是否启用高级选项。 
    1.  设置节点 Pod 数量，是指单个节点可运行 Pod 数量的上限，建议保持默认值。

        ![节点Pod](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15640474659045_zh-CN.png)

    2.  设置kube-proxy代理模式，支持iptables和IPVS两种模式。

        ![Kube-proxy代理](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/149635/156404746541576_zh-CN.png)

        -   iptables：成熟稳定的kube-proxy代理模式，Kubernetes service的服务发现和负载均衡使用iptables规则配置，但性能一般，受规模影响较大，适用于集群存在少量的service。
        -   IPVS：高性能的kube-proxy代理模式，Kubernetes service的服务发现和负载均衡使用Linux ipvs模块进行配置，适用于集群存在大量的service，对负载均衡有高性能要求的场景。
    3.  是否启用工作流引擎。
22. 单击**创建集群**，在弹出的当前配置确认页面，单击**创建**，启动部署。 

    **说明：** 一个包含多节点的 Kubernetes 集群的创建时间一般约为 5 分钟。


集群创建成功后，您可以在容器服务管理控制台的 Kubernetes 集群列表页面查看所创建的集群。

![集群列表](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156404746521425_zh-CN.png)

您可以单击操作列的**查看日志**，进入集群日志信息页面查看集群的日志信息。 您也可以在集群日志信息页面中，单击**资源栈事件**查看更详细的信息。

![集群日志详情](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156404746621426_zh-CN.png)

在集群列表页面中，找到刚创建的集群，单击操作列中的**管理**，查看集群的基本信息和连接信息。

![集群基本信息](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156404746621427_zh-CN.png)

其中：

-   **API Server 公网连接端点**：Kubernetes 的 API Server 对公网提供服务的地址和端口，可以通过此服务在用户终端使用 kubectl 等工具管理集群。
-   **API Service 内网连接端点**：Kubernetes 的 API server 对集群内部提供服务的地址和端口，此 IP 为负载均衡的地址。
-   **Pod网络CIDR**：Kubernetes的Pod CIDR定义集群内Pod的网段范围。
-   **Service CIDR**：Kubernetes的Service CIDR定义集群内暴露服务的网段范围。
-   **测试域名**：为集群中的服务提供测试用的访问域名。服务访问域名后缀是`<cluster_id>.<region_id>.alicontainer.com`。
-   **kube-proxy 代理模式**：Kubernetes service的服务发现和负载均衡需要通过服务代理进行配置，支持iptables和IPVS两种模式。
-   **节点 Pod 数量**：单个节点可运行 Pod 数量的上限，默认值为128。

您可以[通过 kubectl 连接 Kubernetes 集群](intl.zh-CN/用户指南/Kubernetes集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)，执行`kubectl get node`查看集群的节点信息。

![集群查看结果](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156404746621438_zh-CN.png)

