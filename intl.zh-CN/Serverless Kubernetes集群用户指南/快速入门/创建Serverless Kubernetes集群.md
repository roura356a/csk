---
keyword: [Serverless集群, 创建Serverless, ASK, Serverless Kubernetes]
---

# 创建Serverless Kubernetes集群

本文介绍如何在容器服务管理控制台快速创建阿里云Serverless Kubernetes（ASK）集群。

-   开通容器服务ACK，且授权默认角色和开通相关云产品。具体操作，请参见[首次使用容器服务Kubernetes版](/intl.zh-CN/快速入门/首次使用容器服务Kubernetes版.md)。
-   登录[弹性容器实例](https://eci.console.aliyun.com)控制台，开通ECI服务。


## 操作步骤

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击页面右上角的**创建集群**。

4.  单击**ASK集群**页签，然后完成集群配置。

    |配置项|描述|
    |---|--|
    |**集群名称**|填写集群的名称。 **说明：** 集群名称应包含1~63个字符，可包含数字、汉字、英文字符或连字符（-）。 |
    |**地域**|选择集群所在的地域。|
    |**Kubernetes版本**|显示当前ASK支持的Kubernetes版本。 |
    |**专有网络**|设置集群的网络。Kubernetes集群仅支持专有网络。支持**自动创建**和**使用已有**的VPC。     -   **自动创建**：集群会自动新建一个VPC，并在VPC中自动创建NAT网关以及配置SNAT规则。
    -   **使用已有**：您可以在已有VPC列表中选择所需的VPC和交换机。如需访问公网，例如下载容器镜像，要配置NAT网关，建议将容器镜像上传到集群所在区域的阿里云镜像服务，并通过内网VPC地址拉取镜像。
详情请参见[使用专有网络](/intl.zh-CN/专有网络和交换机/使用专有网络.md)。 |
    |**可用区**|选择集群所在的可用区。|
    |**配置SNAT**|设置是否为专有网络创建NAT网关并配置SNAT规则。 仅当**专有网络**选择为**自动创建**时，需要设置该选项。

**说明：** 若您选择**自动创建**VPC，可选择是否自动配置SNAT网关。若选择不自动配置SNAT，您可自行配置NAT网关实现VPC安全访问公网环境，并且手动配置SNAT，否则VPC内实例将不能正常访问公网。

详情请参见[创建NAT网关实例](/intl.zh-CN/基本功能操作/创建NAT网关实例.md)。 |
    |**Service CIDR**|设置**Service CIDR**。您需要指定**Service CIDR**，网段不能与VPC及VPC内已有Kubernetes集群使用的网段重复，创建成功后不能修改。而且Service地址段也不能和Pod地址段重复，有关Kubernetes网络地址段规划的信息，请参见[Kubernetes集群网络规划](/intl.zh-CN/Kubernetes集群用户指南/网络/Kubernetes集群网络规划.md)。 |
    |**API Server访问**|ASK默认为API Server创建一个内网SLB实例，您可修改SLB实例规格。更多信息，请参见[实例规格](/intl.zh-CN/传统型负载均衡CLB/CLB用户指南/实例/CLB实例概述.md)。

**说明：** 删除默认创建的SLB实例将会导致无法访问API Server。

您可设置是否开放**使用EIP暴露API Server**。API Server提供了各类资源对象（Pod，Service等）的增删改查及Watch等HTTP Rest接口。

    -   如果选择开放，ASK会创建一个EIP，并挂载到SLB上。此时，Kubernetes API服务（即API Server）会通过EIP的6443端口暴露出来，您可以在外网通过kubeconfig连接并操作集群。
    -   如果选择不开放，则不会创建EIP，您只能在VPC内部用kubeconfig连接并操作集群。
更多信息，请参见[控制集群API Server的公网访问能力](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/集群访问控制/控制集群API Server的公网访问能力.md)。 |
    |**服务发现**|设置集群的服务发现，支持**不开启**、**PrivateZone**和**CoreDNS**三种方式。**说明：**

    -   PrivateZone：基于阿里云专有网络VPC环境的私有DNS服务。该服务允许您在自定义的一个或多个VPC中将私有域名映射到IP地址。
    -   CoreDNS：是一个灵活可扩展的DNS服务器，也是Kubernetes标准的服务发现组件。 |
    |**Ingress**|设置是否安装Ingress组件。Ingress支持**不安装**、**Nginx Ingress**和**ALB Ingress**三种方式。

    -   Nginx Ingress：基于社区版的ingress-nginx进行了优化，为您的Kubernetes集群提供灵活可靠的路由服务（Ingress）。更多信息，请参见[Nginx Ingress概述](/intl.zh-CN/Serverless Kubernetes集群用户指南/网络/Nginx Ingress管理/Nginx Ingress概述.md)。
    -   ALB Ingress：基于阿里云应用型负载均衡 ALB（Application Load Balancer） 之上提供更为强大的Ingress流量管理方式，兼容Nginx Ingress，具备处理复杂业务路由和证书自动发现的能力，支持HTTP、HTTPS和QUIC协议，完全满足在云原生应用场景下对超强弹性和大规模七层流量处理能力的需求。更多信息，请参见[ALB Ingress概述](/intl.zh-CN/Serverless Kubernetes集群用户指南/网络/ALB Ingress管理/ALB Ingress概述.md)。 |
    |**监控服务**|设置是否安装metrics-server集群基础监控组件。metrics-server是阿里云容器服务Kubernetes版基于社区开源监控组件进行改造和增强的离线监控数据组件，提供查看集群离线监控数据功能，提供HPA和基础资源监控的能力。您可以通过Metrics API获取到采集的监控数据。|
    |**日志服务**|设置是否启用日志服务，您可使用已有Project或新建一个Project。 不开启日志服务时，将无法使用集群审计功能。日志服务详情请参见[快速入门](/intl.zh-CN/.md)。 |
    |**Knative**|设置是否开启Knative。Knative是一款基于Kubernetes的Serverless框架，其目标是制定云原生、跨平台的Serverless编排标准，详细介绍请参见[概述](/intl.zh-CN/Serverless Kubernetes集群用户指南/Knative/概述.md)。|
    |**时区**|选择集群所要使用的时区。默认时区为浏览器所配置的时区。 |
    |**集群删除保护**|设置是否启用集群删除保护。为防止通过控制台或API误释放集群。|
    |**资源组**|将鼠标悬浮于页面上方的**账号全部资源**，选择资源组。在控制台页面顶部选择的资源组可过滤出该资源组内的专有网络及对应的虚拟交换机。在创建集群时，只显示过滤的专有网络实例及专有网络对应的虚拟交换机实例。

![资源组](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/8029478261/p127165.png)

将鼠标悬浮于页面上方的**账号全部资源**，选择集群所在的资源组。这里显示选择的资源组。|
    |**标签**|为集群绑定标签。输入键和对应的值，单击**添加**。

**说明：**

    -   键是必需的，而值是可选的，可以不填写。
    -   键不能是aliyun、http:// 、https:// 开头的字符串，不区分大小写，最多64个字符。
    -   值不能是http:// 或https://，可以为空，不区分大小写，最多128个字符。
    -   同一个资源，标签键不能重复，相同标签键（Key）的标签会被覆盖。
    -   如果一个资源已经绑定了20个标签，已有标签和新建标签会失效，您需要解绑部分标签后才能再绑定新的标签。 |
    |**服务协议**|创建集群前，需阅读并选中**《无服务器Kubernetes服务协议》**。|

5.  在页面右侧，单击**创建集群**，在弹出的当前配置确认页面，单击**确定**，启动部署。


-   集群创建成功后，您可以在容器服务管理控制台的Kubernetes集群列表页面查看所创建的Serverless集群。

![创建集群](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/3645388261/p70347.png)

-   在集群列表页面中，找到刚创建的集群，单击操作列中的**详情**，单击**基本信息**和**连接信息**页签，查看集群的基本信息和连接信息。

    ![基本信息](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/3645388261/p135811.png)


