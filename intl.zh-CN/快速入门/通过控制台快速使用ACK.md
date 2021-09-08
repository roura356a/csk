# 通过控制台快速使用ACK

阿里云容器服务Kubernetes版（简称容器服务ACK）提供高性能的容器化应用管理服务，让您轻松高效地在云端运行Kubernetes容器化应用。本文将指导您如何通过控制台在ACK集群中快速部署并公开一个容器化Demo应用，并监控应用的运行情况。

-   本教程中所使用的Demo应用ACK-Cube为一个线上魔方游戏，该游戏将通过容器镜像部署到ACK Pro版集群中。完成本教程后，您将得到一个ACK Pro版集群和魔方游戏应用。

    ![cube](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/2613290361/p324047.png)

-   Demo应用的容器镜像基于[开源项目](https://github.com/bsehovac/the-cube)构建，镜像地址为`registry.cn-hangzhou.aliyuncs.com/acr-toolkit/ack-cube:1.0`。
-   ACK标准版和Pro版都是托管集群。相比于标准版，本教程所创建的Pro版集群具有更高的可靠性和安全性，并提供可赔付的SLA。关于ACK集群及其所使用云资源的收费情况，请参见[产品计费](/intl.zh-CN/.md)。

## 前提条件

已了解Kubernetes的相关基本概念，请参见[与原生Kubernetes名词对照]()。

## 操作流程

![workflow](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/0098190361/p323721.png)

## 步骤一：开通并授权容器服务ACK

首次使用时，您需要开通容器服务ACK，并为其授权相应云资源的访问权限。

1.  登录[容器服务ACK开通页面](https://common-buy-intl.alibabacloud.com/?commodityCode=csk_propayasgo_public_intl)。

2.  阅读并选中**容器服务ACK服务协议**。

3.  单击**立即开通**。

4.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

5.  在**容器服务需要创建默认角色**页面，单击**前往RAM进行授权**进入[云资源访问授权](https://ram.console.aliyun.com/#/role/authorize?request=%7B%22ReturnUrl%22:%22https:%2F%2Fcs.console.aliyun.com%2F%22,%22Service%22:%22CS%22,%22Requests%22:%7B%22request1%22:%7B%22RoleName%22:%22AliyunCSManagedLogRole%22,%22TemplateId%22:%22AliyunCSManagedLogRole%22%7D,%22request2%22:%7B%22RoleName%22:%22AliyunCSManagedCmsRole%22,%22TemplateId%22:%22AliyunCSManagedCmsRole%22%7D,%22request3%22:%7B%22RoleName%22:%22AliyunCSManagedCsiRole%22,%22TemplateId%22:%22AliyunCSManagedCsiRole%22%7D,%22request4%22:%7B%22RoleName%22:%22AliyunCSManagedVKRole%22,%22TemplateId%22:%22AliyunCSManagedVKRole%22%7D,%22request5%22:%7B%22RoleName%22:%22AliyunCSClusterRole%22,%22TemplateId%22:%22Cluster%22%7D,%22request6%22:%7B%22RoleName%22:%22AliyunCSServerlessKubernetesRole%22,%22TemplateId%22:%22ServerlessKubernetes%22%7D,%22request7%22:%7B%22RoleName%22:%22AliyunCSKubernetesAuditRole%22,%22TemplateId%22:%22KubernetesAudit%22%7D,%22request8%22:%7B%22RoleName%22:%22AliyunCSManagedNetworkRole%22,%22TemplateId%22:%22AliyunCSManagedNetworkRole%22%7D,%22request9%22:%7B%22RoleName%22:%22AliyunCSDefaultRole%22,%22TemplateId%22:%22Default%22%7D,%22request10%22:%7B%22RoleName%22:%22AliyunCSManagedKubernetesRole%22,%22TemplateId%22:%22ManagedKubernetes%22%7D,%22request11%22:%7B%22RoleName%22:%22AliyunCSManagedArmsRole%22,%22TemplateId%22:%22AliyunCSManagedArmsRole%22%7D%7D%7D)页面，然后单击**同意授权**。

    完成以上授权后，刷新控制台即可使用容器服务ACK。


## 步骤二：创建ACK Pro版集群

本步骤指导您如何快速创建一个ACK Pro版集群，因此参数配置大多以默认为主。关于创建集群的详细参数描述，请参见[创建ACK Pro版集群](/intl.zh-CN/Kubernetes集群用户指南/ACK Pro集群/创建ACK Pro版集群.md)。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击页面右上角的**创建集群**。

4.  在**ACK托管版**页签下，配置以下相关的集群参数，未说明参数保留默认设置即可。

    ![集群配置](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/0098190361/p323737.png)

    |参数|说明|
    |--|--|
    |集群名称|填写集群的名称。示例：ACK-Demo。|
    |集群规格|选择集群规格。示例：Pro版。关于ACK Pro版集群的详细信息，请参见[ACK Pro版集群概述](/intl.zh-CN/Kubernetes集群用户指南/ACK Pro集群/ACK Pro版集群介绍.md)。|
    |地域|选择集群所在的地域。示例：华北2（北京）。|
    |专有网络|Kubernetes集群仅支持运行于专有网络，因此您需要为集群指定专有网络VPC，且该VPC必须与集群处于同一地域。示例：在华北2（北京）地域下创建名为vpc-ack-demo的VPC。通过单击**创建专有网络**进行创建，详情请参见[使用专有网络](/intl.zh-CN/专有网络和交换机/使用专有网络.md)。 |
    |虚拟交换机|选择用于集群节点间通信的交换机。示例：在vpc-ack-demo的VPC下创建一个名为vswitch-ack-demo的虚拟交换机，并选择使用该交换机。通过单击**创建虚拟交换机**进行创建，详情请参见[使用交换机](/intl.zh-CN/专有网络和交换机/使用交换机.md)。 |
    |API Server访问|设置集群API Server是否可在公网访问，当您需要从公网远程管理集群时，需要配置弹性公网IP（EIP）。示例：选中**使用EIP暴露API Server**。 |

5.  单击**下一步：Worker配置**，选配Worker节点，未说明参数保留默认设置即可。

    ![worker配置](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/0098190361/p323745.png)

    |参数|说明|
    |--|--|
    |实例规格|为集群选配所使用的Worker节点。为了保证集群的稳定性，建议的实例规格为：vCPU ≥ 4核，内存 ≥ 8 GiB。关于如何选型以及规格介绍，请参见[ECS选型](https://help.aliyun.com/document_detail/98886.html)和[实例规格族](/intl.zh-CN/实例/实例规格族.md)。示例：搜索实例规格ecs.g5.xlarge，并选用该规格的节点。 |
    |数量|根据需要设置集群的Worker节点数量。示例：2个，防止单点故障。|
    |系统盘|选择Worker节点所使用的系统盘。示例：ESSD云盘，40 GiB（最小规格）。|
    |登录方式|选择登录Worker节点的方式，并输入密码。示例：设置密码。|

6.  单击**下一步：组件配置**，所有组件使用默认配置。

7.  单击**下一步：确认配置**，然后选中并阅读**服务协议**，单击**创建集群**。

    **说明：** 集群的创建时间一般约为10分钟。创建完成后，在集群列表页面，可以看到新创建的集群。


## 步骤三：部署并公开应用

本步骤指导您如何在新创建的ACK集群中快速部署一个无状态应用（Deployment），即魔方游戏，并将该应用向公网公开。关于创建Deployment的详细参数描述，请参见[创建无状态工作负载Deployment](/intl.zh-CN/Kubernetes集群用户指南/应用/工作负载/创建无状态工作负载Deployment.md)。

1.  在集群列表页面中，单击目标集群名称（即ACK-Demo）。

2.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

3.  在**无状态**页面中，单击**使用镜像创建**。

4.  在**应用基本信息**页签，设置应用名称为ack-cube。

5.  单击**下一步**，在**容器配置**页签，配置容器的相关参数。

    ![容器配置](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/0098190361/p323776.png)

    |参数|说明|
    |--|--|
    |镜像名称|直接输入不包含镜像Tag的镜像地址或通过单击选择镜像来选择所需的镜像。示例：输入`registry.cn-hangzhou.aliyuncs.com/acr-toolkit/ack-cube` |
    |镜像Tag|单击**选择镜像Tag**选择镜像的版本。若不指定，默认为最新版。示例：1.0。|
    |资源限制|根据需要为该应用指定所能使用的资源上限，防止占用过多资源。示例：1 Core，内存 1024 MiB，Ephemeral Storage为空。 |
    |所需资源|根据需要为该应用指定预留的资源额度，防止因资源不足而导致应用不可用。示例：0.5 Core，内存 512 MiB，Ephemeral Storage为空。 |
    |端口|设置容器的端口。示例：ack-cube，80，TCP。|

6.  单击**下一步**，在**高级配置**页签，单击**服务（Service）**右侧的**创建**。

7.  在**创建服务**对话框中，设置服务的相关参数，单击**创建**，以通过该服务公开ack-cube应用。

    ![service](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/0098190361/p323789.png)

    |参数|说明|
    |--|--|
    |名称|输入服务的名称。示例：ack-cube-svc|
    |类型|选择服务类型，即服务访问的方式。依次选择**负载均衡** -\> **公网访问** -\> **新建SLB** -\> 单击**修改**选择所需的SLB规格。示例：使用默认规格，简约型I（slb.s1.small）。 |
    |端口|设置服务端口和容器端口。容器端口需要与后端的Pod中暴露的容器端口一致。示例：皆为80。 |

8.  在**高级配置**页签，单击页面右下角的**创建**。

    创建成功后，默认进入创建完成页面，会列出应用包含的对象，您可以单击查看应用详情进行查看。

    ![succeed](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/0098190361/p323793.png)


## 步骤四：测试应用

本步骤指导您如何通过服务（Service）来访问新部署的容器化应用。

1.  在集群列表页面中，单击目标集群名称（即ACK-Demo）。

2.  在集群管理页左侧导航栏中，选择**网络** \> **服务**。

3.  在服务列表页面，找到新创建的服务（即ack-cube-svc），单击**外部端点**列的IP地址，即可访问魔方游戏。

    ![service endpoint](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/0098190361/p324011.png)


## 步骤五：监控应用

本步骤指导您如何监控应用的运行状况，如CPU利用率、内存利用率、网络I/O压力等指标。

1.  在集群列表页面中，单击目标集群名称（即ACK-Demo）。

2.  在集群管理页左侧导航栏中，选择**运维管理** \> **Prometheus监控**。

3.  在**Prometheus监控**页面，单击**Deployment**页签，选择**命名空间**为default，选择**deployment**为ack-cube。

    您可以查看应用的资源使用情况，包括创建应用时所设置的资源阈值，所需资源（对应图中request）和资源限制（对应图中limit）。

    ![prometheus-deploy](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/0098190361/p324014.png)

4.  在**Prometheus监控**页面，单击**Pod**页签，选择**命名空间**为default，选择**Pod**为待监控Pod。

    您可以查看单个Pod的资源使用情况。

    ![prometheus-pod](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/0098190361/p324016.png)


## 相关文档

-   为了保证应用能够动态调整所需容器资源，您可以配置容器水平伸缩（HPA）、定时容器水平伸缩（CronHPA）、容器垂直伸缩（HPA）等，详情请参见[弹性伸缩概述](/intl.zh-CN/Kubernetes集群用户指南/弹性伸缩/弹性伸缩概述.md)。
-   除了通过服务（Service）公开应用，您还可以通过路由（Ingress）实现对应用的七层网络路由控制，详情请参见[创建Ingress路由](/intl.zh-CN/Kubernetes集群用户指南/网络/Ingress管理/创建Ingress路由.md)。
-   除了观测容器性能，您还可以观测集群基础设施、应用性能和用户业务，详情请参见[可观测性体系概述](/intl.zh-CN/Kubernetes集群用户指南/可观测性/可观测性体系概述.md)。
-   为了避免产生不必要的费用，请及时清理不需要的集群，详情请参见[删除集群](/intl.zh-CN/Kubernetes集群用户指南/集群/管理集群/删除集群.md)。

