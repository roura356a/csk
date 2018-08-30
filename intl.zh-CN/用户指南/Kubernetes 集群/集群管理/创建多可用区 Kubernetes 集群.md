# 创建多可用区 Kubernetes 集群 {#task_hyb_xwf_vdb .task}

-   您需要开通容器服务、资源编排（ROS）服务和访问控制（RAM）服务。

    登录 [容器服务管理控制台](https://cs.console.aliyun.com/)、[ROS 管理控制台](https://ros.console.aliyun.com/) 和 [RAM 管理控制台](https://ram.console.aliyun.com/) 开通相应的服务。

    **说明：** 容器服务 Kubernetes 集群部署依赖阿里云资源编排 ROS 的应用部署能力，所以创建 Kubernetes 集群前，您需要开通 ROS。

-   您需要先创建一个 VPC 并在该 VPC 中至少创建三个 VSwitch。为了达到高可用的效果，建议您将 VSwitch 创建在不同的可用区。
-   你需要给VPC下的每个vSwtich都收动配置SNAT，否则VPC内实例不能正常访问公网。

您可以通过容器服务管理控制台创建包含不同可用区 ECS 实例的 Kubernetes 集群从而实现高可用。

**使用须知**

创建集群过程中，容器服务会进行如下操作：

-   创建 ECS，配置管理节点到其他节点的 SSH 的公钥登录，通过 CloudInit 安装配置 Kubernetes 集群。
-   创建安全组，该安全组允许 VPC 入方向全部 ICMP 端口的访问。
-   创建 RAM 子账号和 AK，该子账号拥有 ECS 的查询、实例创建和删除的权限，添加和删除云盘的权限，SLB 的全部权限，云监控的全部权限，VPC 的全部权限，日志服务的全部权限，NAS 的全部权限。Kubernetes 集群会根据用户部署的配置相应的动态创建 SLB，云盘，VPC路由规则。
-   创建内网 SLB，暴露 6443 端口。
-   创建公网 SLB，暴露 6443、8443和 22 端口（如果您在创建集群时选择开放公网 SSH 登录，会暴露 22 端口；如果选择不开放公网 SSH 登录，则不会暴露 22 端口）。

**使用限制**

-   随集群一同创建的负载均衡实例只支持按量付费的方式。
-   Kubernetes 集群仅支持专有网络 VPC。
-   每个账号默认可以创建的云资源有一定的配额，如果超过配额创建集群会失败。请在创建集群前确认您的配额。如果您需要提高您的配额，请提交工单申请。
    -   每个账号默认最多可以创建 5 个集群（所有地域下），每个集群中最多可以添加 40 个节点。如果您需要创建更多的集群或者节点，请提交工单申请。
    -   每个账号默认最多可以创建 100 个安全组。
    -   每个账号默认最多可以创建 60 个按量付费的负载均衡实例。
-   ECS 实例使用限制：
    -   仅支持 CentOS 操作系统。
    -   仅支持创建按量付费的 ECS 实例。

        **说明：** 实例创建后，您可以通过 ECS 管理控制台将[按量付费转预付费](../../../../intl.zh-CN/产品定价/按量付费转预付费.md#)。


1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com/?spm=a2c4g.11186623.2.7.1PrXU7#/overview/all)。 
2.  在 Kubernetes 菜单下， 单击左侧导航栏中的**集群**，进入 Kubernetes 集群列表页面。单击页面右上角的**创建Kubernetes 集群**。 
3.  在创建Kubernetes集群页面，选择**多可用区Kubernetes**。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/15356101808748_zh-CN.png)

4.  填写集群的名称。 

    集群名称应包含 1~63 个字符，可包含数字、汉字、英文字符或连字符（-）。

5.  选择集群所在的地域。 
6.  选择专有网络。 

    在已有 VPC 列表中选择一个 VPC 并在该 VPC 下选择三个交换机。为了达到高可用的效果，建议您选择位于不同可用区的交换机。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/15356101808749_zh-CN.png)

7.  设置 Master 节点和 Worker 节点的配置信息。 

    您需要选择 Master 节点和 Worker 节点的系列和规格，以及需要创建的 Worker 节点的数量。

    **说明：** 

    -   目前仅支持 CentOS 操作系统。
    -   目前仅支持创建 3 个 Master 节点。
    -   每个集群最多可包含 37 个 Worker 节点。如果您需要创建更多节点，请提交工单申请。
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/15356101808750_zh-CN.png)

8.  配置登录方式。 
    -   设置密钥。

        您需要在创建集群的时候选择密钥对登录方式，单击**新建密钥对**，跳转到ECS云服务器控制台，创建密钥对，参见[创建 SSH 密钥对](../../../../intl.zh-CN/用户指南/密钥对/创建 SSH 密钥对.md#)。密钥对创建完毕后，设置该密钥对作为登录集群的凭据。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/15356101808751_zh-CN.png)

    -   设置密码。
        -   **登录密码**：设置节点的登录密码。
        -   **确认密码**：确认设置的节点登录密码。
9.  指定**Pod 网络 CIDR** 和**Service CIDR**。 

    两者都不能与 VPC 及 VPC 内已有 Kubernetes 集群使用的网段重复，创建成功后不能修改。而且 Service 地址段也不能和 Pod 地址段重复，有关 kubernetes 网络地址段规划的信息，请参考[VPC下 Kubernetes 的网络地址段规划](intl.zh-CN/用户指南/Kubernetes 集群/集群管理/VPC下 Kubernetes 的网络地址段规划.md#)。

10. 设置是否开放公网 SSH 登录。 

    -   选择开放公网 SSH 登录，您可以 SSH 访问集群。
    -   选择不开放公网 SSH 登录，将无法通过 SSH 访问集群，也无法通过 kubectl 连接 集群。如果您需要通过 SSH 访问集群实例，可以手动为 ECS 实例绑定 EIP，并配置安全组规则，开放 SSH（22）端口，具体操作参见[SSH访问Kubernetes集群](intl.zh-CN/用户指南/Kubernetes 集群/集群管理/SSH访问Kubernetes集群.md#)。
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/15356101818752_zh-CN.png)

11. 设置是否启用云监控插件。 

    您可以选择在 ECS 节点上安装云监控插件，从而在云监控控制台查看所创建 ECS 实例的监控信息。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/15356101818753_zh-CN.png)

12. 设置是否启用高级选项。 
    1.  设置节点 Pod 数量，是指单个节点可运行 Pod 数量的上限。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/15356101818754_zh-CN.png)

    2.  设置是否选择**自定义镜像**。或不选择自定义镜像，则 ECS 实例会安装默认的 CentOS 版本。

        目前您只能选择基于 CentOS 的自定义镜像来快速部署您需要的环境。

    3.  设置是否使用**自定义集群CA**。如果勾选自定义集群 CA，可以将 CA 证书添加到 kubernetes 集群中，加强服务端和客户端之间信息交互的安全性。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/15356101818755_zh-CN.png)

13. 单击**创建集群**，在弹出的配置确认对话框中，确认VPC公网访问，最后单击**确定**，启动部署。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/15356101818759_zh-CN.png)

    **说明：** 一个包含多节点的 Kubernetes 集群的创建时间一般需要十几分钟。


查看集群部署结果

集群创建成功后，您可以在容器服务管理控制台的 Kubernetes 集群列表页面查看所创建的集群。

您可以单击右侧的**查看日志**查看集群的日志信息。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/15356101818756_zh-CN.png)

您可以单击右侧的**查看日志**查看集群的日志信息，您可单击**资源栈事件**查看更详细的信息。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/15356101818756_zh-CN.png)

**其中：**

-   **API Server 公网连接端点：**Kubernetes 的 API server 对公网提供服务的地址和端口，可以通过此服务在用户终端使用 kubectl 等工具管理集群。
-   **API Server 内网连接端点：**Kubernetes 的 API server 对集群内部提供服务的地址和端口。此 IP 为负载均衡的地址，后端有 3 台 Master 提供服务。
-   **Master 节点 SSH 连接地址：**可以直接通过 SSH 登录到 Master 节点，以便对集群进行日常维护。
-   **服务访问域名：**为集群中的服务提供测试用的访问域名。服务访问域名后缀是 `<cluster_id>.<region_id>.alicontainer.com`。

