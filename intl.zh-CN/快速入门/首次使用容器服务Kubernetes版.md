---
keyword: [容器服务ACK, k8s, 开通服务, 角色授权]
---

# 首次使用容器服务Kubernetes版

您首次使用容器服务Kubernetes版ACK（Container Service for Kubernetes）时，无需开通ACK服务，但需要为服务账号授予名称为AliyunCSDefaultRole、AliyunCSServerlessKubernetesRole、AliyunCSClusterRole、AliyunCSManagedKubernetesRole和KubernetesAuditRole的系统默认角色。当且仅当该角色被正确授予后，容器服务才能正常地调用相关服务（ECS，OSS、NAS、SLB等），创建集群以及保存日志等。本文介绍如何给服务账号进行授权及授权角色包含的权限。

## 使用说明

-   如果您是在2018年1月15日之前使用过容器服务的用户，系统将默认完成角色授权，详细的授权权限内容请参见[t16635.md\#section\_rzm\_v4r\_306](/intl.zh-CN/Kubernetes集群用户指南/授权管理/容器服务默认角色.md)。如果您之前是通过子账号使用，您需要对子账号进行策略升级，请参见[自定义RAM授权策略](/intl.zh-CN/Kubernetes集群用户指南/授权管理/自定义RAM授权策略.md)。
-   2018年1月15日容器服务全面接入跨服务授权后，新用户使用主账号只有进行了跨服务授权才能使用容器服务产品。如果新用户需要授权子账号使用容器服务，需要自行前往RAM控制台进行授权，详细操作请参见[使用子账号](/intl.zh-CN/Kubernetes集群用户指南/授权管理/使用子账号（RAM用户）.md)。

## 操作步骤

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  单击**前往RAM进行授权**进入[云资源访问授权](https://ram.console.aliyun.com/#/role/authorize?request=%7B%22ReturnUrl%22:%22https:%2F%2Fcs.console.aliyun.com%2F%22,%22Service%22:%22CS%22,%22Requests%22:%7B%22request1%22:%7B%22RoleName%22:%22AliyunCSManagedLogRole%22,%22TemplateId%22:%22AliyunCSManagedLogRole%22%7D,%22request2%22:%7B%22RoleName%22:%22AliyunCSManagedCmsRole%22,%22TemplateId%22:%22AliyunCSManagedCmsRole%22%7D,%22request3%22:%7B%22RoleName%22:%22AliyunCSManagedCsiRole%22,%22TemplateId%22:%22AliyunCSManagedCsiRole%22%7D,%22request4%22:%7B%22RoleName%22:%22AliyunCSManagedVKRole%22,%22TemplateId%22:%22AliyunCSManagedVKRole%22%7D,%22request5%22:%7B%22RoleName%22:%22AliyunCSClusterRole%22,%22TemplateId%22:%22Cluster%22%7D,%22request6%22:%7B%22RoleName%22:%22AliyunCSServerlessKubernetesRole%22,%22TemplateId%22:%22ServerlessKubernetes%22%7D,%22request7%22:%7B%22RoleName%22:%22AliyunCSKubernetesAuditRole%22,%22TemplateId%22:%22KubernetesAudit%22%7D,%22request8%22:%7B%22RoleName%22:%22AliyunCSManagedNetworkRole%22,%22TemplateId%22:%22AliyunCSManagedNetworkRole%22%7D,%22request9%22:%7B%22RoleName%22:%22AliyunCSDefaultRole%22,%22TemplateId%22:%22Default%22%7D,%22request10%22:%7B%22RoleName%22:%22AliyunCSManagedKubernetesRole%22,%22TemplateId%22:%22ManagedKubernetes%22%7D,%22request11%22:%7B%22RoleName%22:%22AliyunCSManagedArmsRole%22,%22TemplateId%22:%22AliyunCSManagedArmsRole%22%7D%7D%7D)页面，然后单击**同意授权**。

    **说明：**

    -   如果您创建的是托管版集群，还需[同意授权KubernetesAuditRole角色](https://ram.console.aliyun.com/?spm=5176.2020520152.0.0.2d5916ddD6wVTY#/role/authorize?request=%7B%22Requests%22:%20%7B%22request1%22:%20%7B%22RoleName%22:%20%22KubernetesAuditRole%22,%20%22TemplateId%22:%20%22ManagedKubernetes%22%7D%7D,%20%22ReturnUrl%22:%20%22https:%2F%2Fcs.console.aliyun.com%22,%20%22Service%22:%20%22CS%22%7D)访问云产品中的资源。
    -   当前托管集群节点默认的WorkerRolePolicy权限较大，为了进一步加强托管集群节点在多租户场景下的安全隔离性，容器服务Kubernetes版ACK（Container Service for Kubernetes）已收敛托管集群节点RAM角色绑定的权限。详情请参见[托管集群节点RAM角色收敛公告](/intl.zh-CN/产品公告/托管集群节点RAM角色收敛公告.md)。
    -   如果您需修改容器服务已经设置好默认的角色权限，请在RAM控制台角色管理中设置。需注意错误的配置可能导致容器服务无法获取到必要的权限。
3.  完成以上授权后，刷新ACK控制台即可使用ACK服务。

    如果您想查看AliyunCSDefaultRole、AliyunCSServerlessKubernetesRole、AliyunCSClusterRole、AliyunCSManagedKubernetesRole和KubernetesAuditRole角色的详细策略信息，可以登录[RAM的控制台](https://ram.console.aliyun.com/)进行查看。


**相关文档**  


[快速创建Kubernetes托管版集群](/intl.zh-CN/快速入门/基础入门/快速创建Kubernetes托管版集群.md)

[使用镜像快速创建无状态Deployment应用](/intl.zh-CN/快速入门/基础入门/使用镜像快速创建无状态Deployment应用.md)

[使用镜像创建有状态StatefulSet应用](/intl.zh-CN/Kubernetes集群用户指南/应用管理/使用镜像创建有状态StatefulSet应用.md)

