---
keyword: [容器服务ACK, k8s, 开通服务, 角色授权]
---

# 首次使用容器服务Kubernetes版

您首次使用容器服务Kubernetes版（Alibaba Cloud Container Service for Kubernetes，简称容器服务ACK）时，需要为服务账号授予系统默认角色。当且仅当该角色被正确授予后，容器服务才能正常地调用相关服务（ECS，OSS、NAS、SLB等），创建集群以及保存日志等。本文介绍在首次使用ACK时如何授权容服务默认角色以及开通相关云产品。

## 步骤一：开通容器服务ACK

容器服务ACK现已正式商用，在创建ACK集群前您需要开通相应服务。操作步骤如下：

1.  登录[容器服务ACK开通页面](https://common-buy-intl.alibabacloud.com/?commodityCode=csk_propayasgo_public_intl)。

2.  阅读并选中**容器服务ACK服务协议**。

3.  单击**立即开通**。


**说明：** 如果您在创建ACK集群前未进行开通容器服务ACK的操作，在创建集群页面的**依赖检查**区域，系统会提示您进行开通容器服务ACK的操作。

## 步骤二：一键授权默认角色

首次登录容器服务ACK时，需要为服务账号授予系统默认角色。步骤如下：

**说明：** 阿里云账号（主账号）和拥有管理权限的RAM用户（子账号）都可以授权系统默认角色。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  单击**前往RAM进行授权**进入[云资源访问授权](https://ram.console.aliyun.com/#/role/authorize?request=%7B%22ReturnUrl%22:%22https:%2F%2Fcs.console.aliyun.com%2F%22,%22Service%22:%22CS%22,%22Requests%22:%7B%22request1%22:%7B%22RoleName%22:%22AliyunCSManagedLogRole%22,%22TemplateId%22:%22AliyunCSManagedLogRole%22%7D,%22request2%22:%7B%22RoleName%22:%22AliyunCSManagedCmsRole%22,%22TemplateId%22:%22AliyunCSManagedCmsRole%22%7D,%22request3%22:%7B%22RoleName%22:%22AliyunCSManagedCsiRole%22,%22TemplateId%22:%22AliyunCSManagedCsiRole%22%7D,%22request4%22:%7B%22RoleName%22:%22AliyunCSManagedVKRole%22,%22TemplateId%22:%22AliyunCSManagedVKRole%22%7D,%22request5%22:%7B%22RoleName%22:%22AliyunCSClusterRole%22,%22TemplateId%22:%22Cluster%22%7D,%22request6%22:%7B%22RoleName%22:%22AliyunCSServerlessKubernetesRole%22,%22TemplateId%22:%22ServerlessKubernetes%22%7D,%22request7%22:%7B%22RoleName%22:%22AliyunCSKubernetesAuditRole%22,%22TemplateId%22:%22KubernetesAudit%22%7D,%22request8%22:%7B%22RoleName%22:%22AliyunCSManagedNetworkRole%22,%22TemplateId%22:%22AliyunCSManagedNetworkRole%22%7D,%22request9%22:%7B%22RoleName%22:%22AliyunCSDefaultRole%22,%22TemplateId%22:%22Default%22%7D,%22request10%22:%7B%22RoleName%22:%22AliyunCSManagedKubernetesRole%22,%22TemplateId%22:%22ManagedKubernetes%22%7D,%22request11%22:%7B%22RoleName%22:%22AliyunCSManagedArmsRole%22,%22TemplateId%22:%22AliyunCSManagedArmsRole%22%7D%7D%7D)页面，然后单击**同意授权**。

3.  完成以上授权后，刷新控制台即可使用容器服务ACK。


## 步骤三：开通相关云产品

容器服务ACK相关功能依赖或关联其他的云资源服务，因此您需要开通相关云产品。

**说明：** 只有阿里云账号（主账号）才可以开通云产品，RAM用户暂不支持开通云产品操作。

使用阿里云账号（即主账号）登录阿里云官网，根据需要开通以下相关云产品。

-   必选项：使用ACK集群的强依赖服务，必须开通。
-   建议项：集群创建和应用管理中常见的依赖服务，建议开通。
-   可选项：根据业务架构和运维需求选择性开通。

|产品名称|开通链接|开通类型|产品说明|
|----|----|----|----|
|VPC专有网络|[https://www.alibabacloud.com/product/vpc](https://www.alibabacloud.com/product/vpc)|必选项|用于构建集群网络环境和路由规则|
|NAT网关服务|[https://www.alibabacloud.com/product/nat](https://www.alibabacloud.com/product/nat)|必选项|用于为集群开启公网访问和公网镜像拉取|
|SLB负载均衡|[https://www.alibabacloud.com/product/server-load-balancer](https://www.alibabacloud.com/product/server-load-balancer)|必选项|用于为集群创建负载均衡|
|ESS弹性伸缩服务|[https://www.alibabacloud.com/product/auto-scaling](https://www.alibabacloud.com/product/auto-scaling)|必选项|用于为集群创建Worker节点和实现自动伸缩|
|ACR容器镜像服务|[https://www.alibabacloud.com/product/container-registry](https://www.alibabacloud.com/product/container-registry)|建议项|用于云原生资产的安全托管和全生命周期管理|
|ECI弹性容器实例|[https://www.alibabacloud.com/products/elastic-container-instance](https://www.alibabacloud.com/products/elastic-container-instance)|建议项|用于部署ASK Serverless集群|
|ASM服务网格|[https://servicemesh.console.aliyun.com/\#/instances](https://servicemesh.console.aliyun.com/#/instances)|建议项|基于服务网格实现多个ACK集群应用的统一流量管理|
|SLS日志服务|[https://www.alibabacloud.com/product/log-service](https://www.alibabacloud.com/product/log-service)|建议项|用于ACK集群组件和应用的日志采集和检索|
|CMS云监控服务|[https://www.alibabacloud.com/product/cloud-monitor](https://www.alibabacloud.com/product/cloud-monitor)|建议项|用于监控集群节点和应用运行状态|
|阿里云Prometheus|[https://arms.console.aliyun.com/\#/home](https://arms.console.aliyun.com/#/home)|建议项|基于Prometheus实现对ACK集群的监控和告警|
|SAS云安全中心|[https://www.alibabacloud.com/product/security-center](https://www.alibabacloud.com/product/security-center)|可选项|用于监控集群应用运行时的安全事件和告警|
|NAS文件存储|[https://www.alibabacloud.com/product/nas](https://www.alibabacloud.com/product/nas)|可选项|基于NAS实现集群应用数据的文件存储方案|
|OSS对象存储|[https://www.alibabacloud.com/product/oss](https://www.alibabacloud.com/product/oss)|可选项|基于OSS实现集群应用数据的对象存储方案|
|KMS密钥管理服务|[https://www.alibabacloud.com/product/kms](https://www.alibabacloud.com/product/kms)|可选项|用于集群应用密钥的管理以及Pro集群开启密钥的落盘加密能力|
|PrivateZone服务|[https://www.alibabacloud.com/products/private-zone](https://www.alibabacloud.com/products/private-zone)|可选项|基于内网DNS服务实现ASK集群应用的域名访问|

## 容器服务默认角色说明

|角色名称|角色说明|
|----|----|
|[AliyunCSDefaultRole](/intl.zh-CN/Kubernetes集群用户指南/授权/容器服务默认角色.mdsection_ju1_tsb_brp)|ACK在集群管控操作中使用该角色访问您在ECS、VPC、SLB、ROS、ESS等服务中的资源。|
|[AliyunCSManagedKubernetesRole](/intl.zh-CN/Kubernetes集群用户指南/授权/容器服务默认角色.mdsection_j4k_173_ejk)|ACK托管版集群默认使用该角色来访问您在ECS、VPC、SLB、ACR等服务中的资源。|
|[AliyunCSServerlessKubernetesRole](/intl.zh-CN/Kubernetes集群用户指南/授权/容器服务默认角色.md)|ASK集群默认使用该角色来访问您在ECS、VPC、SLB、PVTZ等服务中的资源。|
|[AliyunCSKubernetesAuditRole](/intl.zh-CN/Kubernetes集群用户指南/授权/容器服务默认角色.md)|ACK托管版集群和ASK集群的审计功能使用该角色来访问您在SLS服务中的资源。|
|[AliyunCSManagedNetworkRole](/intl.zh-CN/Kubernetes集群用户指南/授权/容器服务默认角色.mdsection_2xh_x0c_se3)|ACK托管版集群和ASK集群的网络组件使用该角色访问您在ECS、VPC服务中的资源。|
|[AliyunCSManagedCsiRole](/intl.zh-CN/Kubernetes集群用户指南/授权/容器服务默认角色.mdsection_ot3_h1u_t4i)|ACK托管版集群和ASK集群的存储组件使用该角色访问您在ECS、NAS服务中的资源。|
|[AliyunCSManagedCmsRole](/intl.zh-CN/Kubernetes集群用户指南/授权/容器服务默认角色.md)|ACK托管版集群和ASK集群的监控组件使用该角色访问您在CMS、SLS服务中的资源。|
|[AliyunCSManagedLogRole](/intl.zh-CN/Kubernetes集群用户指南/授权/容器服务默认角色.mdsection_q0d_zoc_k5u)|ACK托管版集群和ASK集群的日志组件使用该角色访问您在SLS服务中的资源。|
|[AliyunCSManagedVKRole](/intl.zh-CN/Kubernetes集群用户指南/授权/容器服务默认角色.mdsection_1np_fxp_gl4)|ASK集群的Virtual Kubelet组件使用该角色访问您在ECS、VPC、ECI等服务中的资源。|
|[AliyunCSManagedArmsRole](/intl.zh-CN/Kubernetes集群用户指南/授权/容器服务默认角色.md)|ACK托管版集群和ASK集群的应用实时监控组件使用该角色访问您在ARMS服务中的资源。|

**相关文档**  


[入门概述](/intl.zh-CN/快速入门/入门指引.md)

[容器服务默认角色](/intl.zh-CN/Kubernetes集群用户指南/授权/容器服务默认角色.md)

[快速创建Kubernetes托管版集群](/intl.zh-CN/快速入门/基础入门/快速创建Kubernetes托管版集群.md)

[使用镜像快速创建无状态Deployment应用](/intl.zh-CN/快速入门/基础入门/使用镜像快速创建无状态Deployment应用.md)

[创建有状态工作负载StatefulSet](/intl.zh-CN/Kubernetes集群用户指南/应用/工作负载/创建有状态工作负载StatefulSet.md)

