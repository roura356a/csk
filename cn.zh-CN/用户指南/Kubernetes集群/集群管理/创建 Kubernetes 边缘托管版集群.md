# 创建 Kubernetes 边缘托管版集群 {#task_skz_qwk_qfb .task}

您可以通过容器服务控制台非常方便的创建 Kubernetes 边缘托管版集群。

您需要开通容器服务、资源编排（ROS）服务、弹性伸缩（ESS）服务和访问控制（RAM）服务。

登录 [容器服务管理控制台](https://cs.console.aliyun.com/)、[ROS 管理控制台](https://ros.console.aliyun.com/)、 [RAM 管理控制台](https://ram.console.aliyun.com/) 和 [弹性伸缩控制台](https://essnew.console.aliyun.com) 开通相应的服务。

**说明：** 容器服务 Kubernetes 边缘托管版集群部署依赖阿里云资源编排 ROS 的应用部署能力，所以创建 Kubernetes 边缘托管版集群前，您需要开通 ROS。

-   用户账户需有 100 元的余额并通过实名认证，否则无法创建按量付费的 ECS 实例和负载均衡。
-   随集群一同创建的负载均衡实例只支持按量付费的方式。
-   Kubernetes 集群仅支持专有网络VPC。
-   每个账号默认可以创建的云资源有一定的配额，如果超过配额创建集群会失败。请在创建集群前确认您的配额。如果您需要提高您的配额，请提交工单申请。
    -   每个账号默认最多可以创建 100 个安全组。
    -   每个账号默认最多可以创建 60 个按量付费的负载均衡实例。
    -   每个账号默认最多可以创建 20 个EIP。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com/?spm=a2c4g.11186623.2.7.1PrXU7#/overview/all)。
2.  在 Kubernetes 菜单下， 单击左侧导航栏中的**集群** \> **集群**，单击页面右上角的**创建 Kubernetes 集群**。
3.  在弹出的选择集群模板页面，选择边缘托管集群并单击**创建**，进入**Kubernetes 边缘托管版**页面。
4.  选择集群所在的资源组。 

    ![资源组](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/855887/156457073651080_zh-CN.png)

5.  填写集群的名称。 

    集群名称应包含 1~63 个字符，可包含数字、汉字、英文字符或连字符（-）。

    ![集群名称](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/855887/156457073651368_zh-CN.png)

6.  选择集群所在的地域和可用区。 

    ![可用区](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/855887/156457073651082_zh-CN.png)

7.  设置集群的网络。Kubernetes 边缘托管版集群仅支持专有网络。 

    您可以在已有 VPC 列表中选择所需的 VPC 和交换机。

    ![专有网络](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/855887/156457073751369_zh-CN.png)

    -   如果您使用的 VPC 中当前已有 NAT 网关，容器服务会使用已有的 NAT 网关。
    -   如果 VPC 中没有 NAT 网关，系统会默认自动为您创建一个 NAT 网关。如果您不希望系统自动创建 NAT 网关，可以取消勾选页面下方的**为专有网络配置 SNAT**。

        **说明：** 若选择不自动创建 NAT 网关，您需要自行配置 NAT 网关实现 VPC 安全访问公网环境，或者手动配置 SNAT，否则 VPC 内实例将不能正常访问公网，会导致集群创建失败。

8.  显示当前支持的Kubernetes版本。 

    ![k8s版本](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/855887/156457073751370_zh-CN.png)

9.  设置**Pod 网络 CIDR** 和**Service CIDR**。 

    ![地址网段](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156457073721265_zh-CN.png)

    **说明：** **Pod 网络 CIDR**和**Service CIDR**两者都不能与 VPC 及 VPC 内已有 Kubernetes 集群使用的网段重复，也不能和边缘节点所在网段重复，创建成功后不能修改。且 Service 地址段也不能和 Pod 地址段重复，有关 Kubernetes 网络地址段规划的信息，请参考[VPC下 Kubernetes 的网络地址段规划](cn.zh-CN/最佳实践/集群/VPC下 Kubernetes 的网络地址段规划.md#)。

10. 设置是否为专有网络配置 SNAT 网关。 

    ![配置SNAT](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156457073721267_zh-CN.png)

    **说明：** 若不勾选**为专有网络配置 SNAT**，您需要自行配置NAT 网关实现 VPC 安全访问公网环境；或者手动配置 SNAT，否则 VPC 内实例将不能正常访问公网，会导致集群创建失败。

11. 设置是否启用公网访问。 

    **说明：** 通常边缘节点需要通过公网和云端API server交互，因此若不勾选**使用 EIP 暴露API Server**，边缘节点将无法连接到云端集群，所创建集群也将无法在边缘场景下使用。

    API Server提供了各类资源对象（Pod，Service等）的增删改查及watch等HTTP Rest接口。

    ![公网访问](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156457073749921_zh-CN.png)

    -   如果选择开放，会创建一个EIP，同时把Master节点的6443端口（对应API Server）暴露出来，用户可以在外网通过kubeconfig连接/操作集群。
    -   若选择不开放，不会创建EIP，用户只能在VPC内部用kubeconfig连接/操作集群。
12. 设置是否启用云监控插件。 勾选**在ECS节点上安装云监控插件**，平台会自动帮助您购买ECS用于部署监控管控组件并会在集群中自动配置云监控插件。

    ![云监控插件](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15645707379044_zh-CN.png)

13. 设置是否启用日志服务，您可使用已有Project或新建一个Project。 

    勾选**使用日志服务**，平台会自动帮助您购买ECS用于部署日志管控组件并会在集群中自动配置日志服务插件。创建应用时，您可通过简单配置，快速使用日志服务，详情参见[使用日志服务进行Kubernetes日志采集](cn.zh-CN/Kubernetes集群用户指南/日志管理/使用日志服务进行Kubernetes日志采集.md#)。

    ![日志服务](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156457073749924_zh-CN.png)

14. 实例配置。 

    **说明：** 当您勾选了**云监控插件**和**日志服务**，此处的ECS配置才会呈现。

    ![实例配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/855887/156457073851371_zh-CN.png)

    -   **实例规格**：支持选择多个实例规格。参见[实例规格族](../../../../cn.zh-CN/实例/实例规格族.md#)。
    -   **数量**：新增 Worker 实例的数量。
    -   **系统盘**：支持 SSD 云盘和高效云盘。
    -   **挂载数据盘**：支持 SSD 云盘、高效云盘和普通云盘。
    **说明：** 

    -   默认为实例挂载系统盘，支持高效云盘和SSD云盘。
    -   支持为实例挂载 1 个数据盘，支持高效云盘和SSD云盘。
15. 设置登录方式。 

    **说明：** 当您勾选**云监控插件**或**日志服务**时，需要给ECS设置登录方式。

    ![登录方式](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156457073821262_zh-CN.png)

    -   设置密钥。

        您需要在创建集群的时候选择密钥对登录方式，单击**新建密钥对**，跳转到ECS云服务器控制台，创建密钥对，参见[创建 SSH 密钥对](../../../../cn.zh-CN/安全/SSH密钥对/使用SSH密钥对.md#)。密钥对创建完毕后，设置该密钥对作为登录集群的凭据。

    -   设置密码。
        -   **登录密码**：设置节点的登录密码。
        -   **确认密码**：确认设置的节点登录密码。
16. 设置RDS白名单。 

    将节点 IP 添加到 RDS 实例的白名单。

    ![RDS白名单](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156457073821270_zh-CN.png)

17. 为集群绑定标签。 

    输入键和对应的值，单击**添加**。

    ![标签](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156457073849925_zh-CN.png)

    **说明：** 

    -   键是必需的，而 值 是可选的，可以不填写。
    -   键 不能是 aliyun、http:// 、https:// 开头的字符串，不区分大小写，最多 64 个字符。
    -   值 不能是 http:// 或 https://，可以为空，不区分大小写，最多 128 个字符。
    -   同一个资源，标签键不能重复，相同标签键（Key）的标签会被覆盖。
    -   如果一个资源已经绑定了 20 个标签，已有标签和新建标签会失效，您需要解绑部分标签后才能再绑定新的标签。
18. 单击**创建集群**，在弹出的当前配置确认页面，单击**创建**，启动部署。 

    **说明：** 一个 Kubernetes 边缘托管版集群的创建时间一般约为 15 分钟。


集群创建成功后，您可以在容器服务管理控制台的 Kubernetes 集群列表页面查看所创建的集群。

![集群列表](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/855887/156457073851372_zh-CN.png)

您可以单击操作列的**查看日志**，进入集群日志信息页面查看集群的日志信息。 您也可以在集群日志信息页面中，单击**资源栈事件**查看更详细的信息。

![集群信息](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/855887/156457073851373_zh-CN.png)

在集群列表页面中，找到刚创建的集群，单击操作列中的**管理**，查看集群的基本信息和连接信息。

![集群详细信息](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/855887/156457073851374_zh-CN.png)

其中：

-   **API Server 公网连接端点**：Kubernetes 的 API Server 对公网提供服务的地址和端口，可以通过此服务在用户终端使用 kubectl 等工具管理集群。
-   **API Service 内网连接端点**：Kubernetes 的 API server 对集群内部提供服务的地址和端口，此 IP 为负载均衡的地址。
-   **Pod网络CIDR**：Kubernetes的Pod CIDR定义集群内Pod的网段范围。
-   **Service CIDR**：Kubernetes的Service CIDR定义集群内暴露服务的网段范围。
-   **测试域名**：为集群中的服务提供测试用的访问域名。服务访问域名后缀是`<cluster_id>.<region_id>.alicontainer.com`。
-   **节点 Pod 数量**：单个节点可运行 Pod 数量的上限，默认值为128。
-   **网络插件**：支持Flannel和Terway网络插件。

您可以[通过 kubectl 连接 Kubernetes 集群](cn.zh-CN/用户指南/Kubernetes集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)，执行`kubectl get node`查看集群的节点信息。

![查看结果](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/855887/156457073951375_zh-CN.png)

