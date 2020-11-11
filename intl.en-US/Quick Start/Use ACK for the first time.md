---
keyword: [Container Service for Kubernetes \(ACK\), Kubernetes, activate ACK, assign roles]
---

# Use ACK for the first time

To use Container Service for Kubernetes \(ACK\) for the first time, you do not need to activate the service. However, you must assign default roles to the service account. After you perform this operation, the service account can be used to call services such as Elastic Compute Service \(ECS\), Object Storage Service \(OSS\), Apsara File Storage NAS, and Server Load Balancer \(SLB\), create clusters, and store logs. This topic describes how to assign roles to the service account.

The default roles in ACK include:

-   [AliyunCSManagedLogRole](#d7e58)
-   [AliyunCSManagedCmsRole](#d7e313)
-   [AliyunCSManagedCsiRole](#d7e410)
-   [AliyunCSManagedVKRole](#d7e630)
-   [AliyunCSClusterRole](#d7e884)
-   [\#d7e1223](#d7e1223)
-   [\#d7e1841](#d7e1841)
-   [AliyunCSManagedNetworkRole](#d7e2040)
-   [\#d7e2106](#d7e2106)
-   [\#d7e3149](#d7e3149)
-   [AliyunCSManagedArmsRole](#d7e3446)

## Considerations

-   If you used ACK before January 15, 2018, the system automatically assigns the default roles to the service account. For more information about role permissions, see [Default roles](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Role authorization.md). If you use a Resource Access Management \(RAM\) user to access ACK, you must upgrade the RAM policy that is attached to the RAM user. For more information, see [Customize RAM policies](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create custom RAM permission policies.md).
-   As of January 15, 2018, new users must assign their Alibaba Cloud accounts the default roles to use ACK. To authorize RAM users to use ACK, a new user must log on to the RAM console. For more information, see [Overview](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Overview.md).

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  If you have not assigned your Alibaba Cloud account the default roles, click **Go to RAM console**. On the [Cloud Resource Access Authorization](https://ram.console.aliyun.com/#/role/authorize?request=%7B%22ReturnUrl%22:%22https:%2F%2Fcs.console.aliyun.com%2F%22,%22Service%22:%22CS%22,%22Requests%22:%7B%22request1%22:%7B%22RoleName%22:%22AliyunCSManagedLogRole%22,%22TemplateId%22:%22AliyunCSManagedLogRole%22%7D,%22request2%22:%7B%22RoleName%22:%22AliyunCSManagedCmsRole%22,%22TemplateId%22:%22AliyunCSManagedCmsRole%22%7D,%22request3%22:%7B%22RoleName%22:%22AliyunCSManagedCsiRole%22,%22TemplateId%22:%22AliyunCSManagedCsiRole%22%7D,%22request4%22:%7B%22RoleName%22:%22AliyunCSManagedVKRole%22,%22TemplateId%22:%22AliyunCSManagedVKRole%22%7D,%22request5%22:%7B%22RoleName%22:%22AliyunCSClusterRole%22,%22TemplateId%22:%22Cluster%22%7D,%22request6%22:%7B%22RoleName%22:%22AliyunCSServerlessKubernetesRole%22,%22TemplateId%22:%22ServerlessKubernetes%22%7D,%22request7%22:%7B%22RoleName%22:%22AliyunCSKubernetesAuditRole%22,%22TemplateId%22:%22KubernetesAudit%22%7D,%22request8%22:%7B%22RoleName%22:%22AliyunCSManagedNetworkRole%22,%22TemplateId%22:%22AliyunCSManagedNetworkRole%22%7D,%22request9%22:%7B%22RoleName%22:%22AliyunCSDefaultRole%22,%22TemplateId%22:%22Default%22%7D,%22request10%22:%7B%22RoleName%22:%22AliyunCSManagedKubernetesRole%22,%22TemplateId%22:%22ManagedKubernetes%22%7D,%22request11%22:%7B%22RoleName%22:%22AliyunCSManagedArmsRole%22,%22TemplateId%22:%22AliyunCSManagedArmsRole%22%7D%7D%7D) page, click **Confirm Authorization Policy**.

    **Note:**

    -   If you use a managed cluster, you must [assign KubernetesAuditRole](https://ram.console.aliyun.com/?spm=5176.2020520152.0.0.2d5916ddD6wVTY#/role/authorize?request=%7B%22Requests%22:%20%7B%22request1%22:%20%7B%22RoleName%22:%20%22KubernetesAuditRole%22,%20%22TemplateId%22:%20%22ManagedKubernetes%22%7D%7D,%20%22ReturnUrl%22:%20%22https:%2F%2Fcs.console.aliyun.com%22,%20%22Service%22:%20%22CS%22%7D) to your account to access your cloud resources.
    -   The default policy WorkerRolePolicy that is attached to worker roles in a managed cluster has high permissions. To ensure data security and resource isolation in multi-tenancy scenarios, ACK reduces the permissions of RAM roles in a managed cluster. For more information, see [Container Service for Kubernetes reduces the permissions of worker RAM roles in managed clusters](/intl.en-US/Bulletin/Container Service for Kubernetes reduces the permissions of worker RAM roles in managed
         clusters.md).
    -   To modify the permission settings of default roles, log on to the RAM console and go to the RAM Roles page. Make sure that ACK is granted the required permissions when you modify the permission settings.
3.  After you assign the default roles to your account, refresh the console to use ACK.


**Related topics**  


[Default roles](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Role authorization.md)

[Create a cluster of ACK Managed Edition](/intl.en-US/Quick Start/Basic operations/Create a cluster of ACK Managed Edition.md)

[Deploy stateless applications from images](/intl.en-US/Quick Start/Basic operations/Deploy stateless applications from images.md)

[Deploy stateful applications from images](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an image to create a stateful application.md)

