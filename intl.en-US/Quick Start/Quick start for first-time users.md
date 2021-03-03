---
keyword: [ACK, Kubernetes, activate cloud services, assign roles]
---

# Quick start for first-time users

You do not need to activate Container Service for Kubernetes \(ACK\) before you get started with ACK. However, if you are a first-time user, you must assign the default roles to your service account before you start. Only after you assign these roles, ACK can use resources in other cloud services to create clusters or save log files. These cloud services include Elastic Compute Service \(ECS\), Object Storage Service \(OSS\), Apsara File Storage NAS \(NAS\), and Server Load Balancer \(SLB\). This topic describes how to assign the default roles to ACK and activate the associated cloud services when you use ACK for the first time.

## Step 1: Assign the default roles

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  If you have not assigned the default roles to your service account, click **Go to RAM console**. On the [Cloud Resource Access Authorization](https://ram.console.aliyun.com/#/role/authorize?request=%7B%22ReturnUrl%22:%22https:%2F%2Fcs.console.aliyun.com%2F%22,%22Service%22:%22CS%22,%22Requests%22:%7B%22request1%22:%7B%22RoleName%22:%22AliyunCSManagedLogRole%22,%22TemplateId%22:%22AliyunCSManagedLogRole%22%7D,%22request2%22:%7B%22RoleName%22:%22AliyunCSManagedCmsRole%22,%22TemplateId%22:%22AliyunCSManagedCmsRole%22%7D,%22request3%22:%7B%22RoleName%22:%22AliyunCSManagedCsiRole%22,%22TemplateId%22:%22AliyunCSManagedCsiRole%22%7D,%22request4%22:%7B%22RoleName%22:%22AliyunCSManagedVKRole%22,%22TemplateId%22:%22AliyunCSManagedVKRole%22%7D,%22request5%22:%7B%22RoleName%22:%22AliyunCSClusterRole%22,%22TemplateId%22:%22Cluster%22%7D,%22request6%22:%7B%22RoleName%22:%22AliyunCSServerlessKubernetesRole%22,%22TemplateId%22:%22ServerlessKubernetes%22%7D,%22request7%22:%7B%22RoleName%22:%22AliyunCSKubernetesAuditRole%22,%22TemplateId%22:%22KubernetesAudit%22%7D,%22request8%22:%7B%22RoleName%22:%22AliyunCSManagedNetworkRole%22,%22TemplateId%22:%22AliyunCSManagedNetworkRole%22%7D,%22request9%22:%7B%22RoleName%22:%22AliyunCSDefaultRole%22,%22TemplateId%22:%22Default%22%7D,%22request10%22:%7B%22RoleName%22:%22AliyunCSManagedKubernetesRole%22,%22TemplateId%22:%22ManagedKubernetes%22%7D,%22request11%22:%7B%22RoleName%22:%22AliyunCSManagedArmsRole%22,%22TemplateId%22:%22AliyunCSManagedArmsRole%22%7D%7D%7D) page, click **Confirm Authorization Policy**.

3.  After you assign the default roles to the service account, log on to the ACK console again to get started with ACK.


## Step 2: Activate the associated cloud services

Some features provided by ACK are reliant on or associated with other cloud services. Therefore, you must activate the cloud services before you can use these features.

Log on to the Alibaba Cloud official website with your Alibaba Cloud account and activate the following cloud services based on your requirements.

-   Required: the services that you must activate. ACK clusters must run on top of these services to function as expected.
-   Recommended: the services that we recommend you to activate. You can choose to use these services when you create ACK clusters and manage applications.
-   Optional: the services that you can activate based on the architecture and the requirements of management and maintenance.

|Service|Service link|Category|Description|
|-------|------------|--------|-----------|
|Virtual Private Cloud \(VPC\)|[https://www.alibabacloud.com/product/vpc](https://www.alibabacloud.com/product/vpc)|Required|This service allows you to build networks and create Ingress rules for ACK clusters.|
|NAT Gateway|[https://www.alibabacloud.com/product/nat](https://www.alibabacloud.com/product/nat)|Required|This service enables ACK clusters to communicate with the Internet and pull images over the Internet.|
|Server Load Balancer \(SLB\)|[https://www.alibabacloud.com/product/server-load-balancer](https://www.alibabacloud.com/product/server-load-balancer)|Required|This service allows you to create load balancing Services for ACK clusters.|
|Auto Scaling \(ESS\)|[https://www.alibabacloud.com/product/auto-scaling](https://www.alibabacloud.com/product/auto-scaling)|Required|This service allows ACK to automatically create worker nodes and enables ACK clusters to automatically scale in or out.|
|Container Registry|[https://www.alibabacloud.com/product/container-registry](https://www.alibabacloud.com/product/container-registry)|Recommended|This service ensures the security of cloud-native applications that are fully managed on the cloud and allows you to manage the lifecycle of these applications.|
|Elastic Container Instance|[https://www.alibabacloud.com/products/elastic-container-instance](https://www.alibabacloud.com/products/elastic-container-instance)|Recommended|After you activate this service, you can deploy serverless Kubernetes \(ASK\) clusters.|
|Alibaba Cloud Service Mesh \(ASM\)|[https://servicemesh.console.aliyun.com/\#/instances](https://servicemesh.console.aliyun.com/#/instances)|Recommended|This service allows you to manage the network traffic of applications that are deployed across multiple ACK clusters by using service meshes.|
|Log Service|[https://www.alibabacloud.com/product/log-service](https://www.alibabacloud.com/product/log-service)|Recommended|This service allows you to collect and query log data of ACK components and applications.|
|Cloud Monitor|[https://www.alibabacloud.com/product/cloud-monitor](https://www.alibabacloud.com/product/cloud-monitor)|Recommended|This service allows you to monitor the status of nodes and applications in ACK clusters.|
|Prometheus Service \(Prometheus\)|[https://arms.console.aliyun.com/\#/home](https://arms.console.aliyun.com/#/home)|Recommended|This service allows you to monitor ACK clusters and raise alerts when anomalies are detected.|
|Security Center \(SAS\)|[https://www.alibabacloud.com/product/security-center](https://www.alibabacloud.com/product/security-center)|Optional|This service allows you to monitor the security events of application runtimes in ACK clusters and raise alerts when anomalies are detected.|
|Apsara File Storage NAS \(NAS\)|[https://www.alibabacloud.com/product/nas](https://www.alibabacloud.com/product/nas)|Optional|This service allows you to store application data in NAS file systems.|
|Object Storage Service \(OSS\)|[https://www.alibabacloud.com/product/oss](https://www.alibabacloud.com/product/oss)|Optional|This service allows you to store application data in OSS buckets.|
|Key Management Service \(KMS\)|[https://www.alibabacloud.com/product/kms](https://www.alibabacloud.com/product/kms)|Optional|This service allows you to manage application Secrets and encrypt Secrets for professional managed Kubernetes clusters.|
|Alibaba Cloud DNS PrivateZone|[https://www.alibabacloud.com/products/private-zone](https://www.alibabacloud.com/products/private-zone)|Optional|Alibaba Cloud DNS PrivateZone is intended for resolving private domain names in VPCs. You can access applications that are deployed in ASK clusters by using this service to resolve the domain names of the applications.|

## ACK default roles

|Name|Description|
|----|-----------|
|[AliyunCSDefaultRole](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/ACK default roles.mdsection_ju1_tsb_brp)|ACK assumes this role to access your resources in other cloud services when it manages ACK clusters. These cloud services include ECS, VPC, SLB, ESS, and Resource Orchestration Service \(ROS\).|
|[AliyunCSManagedKubernetesRole](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/ACK default roles.mdsection_j4k_173_ejk)|By default, a managed Kubernetes cluster assumes this role to access your resources in other cloud services. These cloud services include ECS, VPC, SLB, and Container Registry.|
|[AliyunCSServerlessKubernetesRole](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/ACK default roles.md)|By default, an ASK cluster assumes this role to access your resources in other cloud services. These cloud services include ECS, VPC, SLB, and Alibaba Cloud DNS PrivateZone.|
|[AliyunCSKubernetesAuditRole](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/ACK default roles.md)|The auditing feature of managed Kubernetes clusters and ASK clusters assumes this role to access your resources in Log Service.|
|[AliyunCSManagedNetworkRole](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/ACK default roles.mdsection_2xh_x0c_se3)|The network plug-in of managed Kubernetes clusters and ASK clusters assumes this role to access your resources in ECS and VPC.|
|[AliyunCSManagedCsiRole](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/ACK default roles.mdsection_ot3_h1u_t4i)|The storage plug-in of managed Kubernetes clusters and ASK clusters assumes this role to access your resources in ECS and NAS.|
|[AliyunCSManagedCmsRole](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/ACK default roles.md)|The monitoring component of managed Kubernetes clusters and ASK clusters assumes this role to access your resources in Cloud Monitor and Log Service.|
|[AliyunCSManagedLogRole](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/ACK default roles.mdsection_q0d_zoc_k5u)|The log component of managed Kubernetes clusters and ASK clusters assumes this role to access your resources in Log Service.|
|[AliyunCSManagedVKRole](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/ACK default roles.mdsection_1np_fxp_gl4)|The Virtual Kubelet component of ASK clusters assumes this role to access your resources in other cloud services. These cloud services include ECS, VPC, and Elastic Container Instance.|
|[AliyunCSManagedArmsRole](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/ACK default roles.md)|The application monitoring component of managed Kubernetes clusters and ASK clusters assumes this role to access your resources in Application Real-Time Monitoring Service \(ARMS\).|

**Related topics**  


[Quick start](/intl.en-US/Quick Start/Quick start.md)

[ACK default roles](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/ACK default roles.md)

[Create a managed Kubernetes cluster](/intl.en-US/Quick Start/Basic operations/Create a managed Kubernetes cluster.md)

[Deploy a stateless application from an image](/intl.en-US/Quick Start/Basic operations/Deploy a stateless application from an image.md)

[Deploy a stateful application from an image](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Deploy a stateful application from an image.md)

