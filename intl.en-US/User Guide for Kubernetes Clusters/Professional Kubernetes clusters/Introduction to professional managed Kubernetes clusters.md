---
keyword: [professional managed Kubernetes clusters, professional managed Kubernetes clusters, introduction to professional managed Kubernetes clusters]
---

# Introduction to professional managed Kubernetes clusters

Professional managed Kubernetes clusters are developed based on standard managed Kubernetes clusters. Professional managed Kubernetes clusters are covered by the service level agreement \(SLA\) that supports compensation clauses. This type of cluster is suitable for enterprise users that require higher stability and security for large-scale production environments.

Professional managed Kubernetes clusters offer all benefits of standard managed Kubernetes clusters. For example, master nodes are highly available and managed by Container Service for Kubernetes \(ACK\). In addition, professional managed Kubernetes clusters provide higher reliability, security, and schedulability and are covered by SLA terms for compensation. Professional managed Kubernetes clusters are suitable for enterprises that require higher security and stability for large-scale workloads in production environments.

## Scenarios

-   Internet enterprises. These enterprises deploy their business on a large scale and require business management with high stability, security, and observability.
-   Big data computing enterprises. These enterprises deploy large-scale data computing services, high-performance data processing services, and other services with high elasticity. These services require clusters with high stability, high performance, and efficient computing capabilities.
-   International enterprises that run their business in China. These enterprises prioritize security and services that provide SLAs with compensation clauses.
-   Financial enterprises. These enterprises require SLAs with compensation clauses.

## Features

-   Managed master nodes with high reliability: The management of large-scale clusters is supported. etcd is a reliable store for disaster recovery and data restoration. etcd uses cold backups and hot backups to ensure data availability for professional managed Kubernetes clusters. Key metrics are collected for you to gain insights from control components. This allows you to detect potential risks.
-   Clusters with higher security: By default, etcd uses encrypted disks in the control plane. In the data plane, kms-plugin is installed to encrypt Kubernetes Secrets. ACK provides security management for this type of cluster. The advanced security management feature allows you to inspect containers in the running state and enable auto repairing.
-   More intelligent pod scheduling: kube-scheduler is integrated to provide better pod scheduling capabilities. This allows you to schedule pods in bulk, set multiple scheduling algorithms, and schedule pods to NPU-accelerated nodes. This also enhances the pod scheduling capability in scenarios where large-scale data computing or high-performance data processing is required.
-   SLA guarantees: Professional managed Kubernetes clusters are covered by the SLA that supports compensation clauses. A level of 99.95% uptime is guaranteed for the cluster API server.

## Pricing

For more information about the pricing of professional managed Kubernetes clusters, see [Billing rules](/intl.en-US/Pricing/Billing rules.md).

## Comparison

The following table compares professional managed Kubernetes clusters with standard managed Kubernetes clusters.

|Category|Feature|Professional managed Kubernetes Cluster|Standard managed Kubernetes cluster|
|--------|-------|---------------------------------------|-----------------------------------|
|Cluster size|N/A|Up to 1,000 nodes by default. You can [Navigate to the Quota Center page to submit a ticket](https://quotas.console.aliyun.com/products/csk/quotas) to increase the quota to 5,000.

|Up to 100 nodes for each new cluster. A maximum of 1,000 nodes can be deployed in an existing standard managed Kubernetes cluster. Existing standard managed Kubernetes clusters can be upgraded to professional managed Kubernetes clusters.|
|SLA|N/A|99.95% \(supports compensation\).|99.90% \(does not support compensation\).|
|API Server|Custom parameter settings|![Supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232205.png)|![Not supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232208.png)|
|Availability monitoring|![Supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232205.png)|![Not supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232208.png)|
|etcd|High-frequency cold backups, high-frequency hot backups, and geo-disaster recovery|![Supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232205.png)|![Not supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232208.png)|
|Observability metrics|![Supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232205.png)|![Not supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232208.png)|
|Kube-scheduler|[Gang scheduling](/intl.en-US/User Guide for Kubernetes Clusters/Scheduling/Workload scheduling/Gang scheduling.md)|![Supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232205.png)|![Not supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232208.png)|
|[Topology-aware CPU scheduling](/intl.en-US/User Guide for Kubernetes Clusters/Scheduling/Resource scheduling/Topology-aware CPU scheduling.md)|![Supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232205.png)|![Not supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232208.png)|
|[Topology-aware GPU scheduling](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/GPU Resource scheduling/GPU topology-aware scheduling/Overview.md)|![Supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232205.png)|![Not supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232208.png)|
|[cGPU Professional Edition](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/GPU Resource scheduling/cGPU Professional Edition/Overview.md)|![Supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232205.png)|![Not supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232208.png)|
|Security management|The advanced security management feature that supports data encryption. For more information, see [Use KMS to encrypt Kubernetes secrets at rest in the etcd](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Use KMS to encrypt Kubernetes secrets at rest in the etcd.md).|![Supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232205.png)|![Not supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232208.png)|
|Managed node pool|[Managed node pool](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Managed node pools/Overview.md)|![Supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232205.png)|![Not supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232208.png)|

**Note:**

Dedicated Kubernetes clusters support only cGPU Basic Edition. For more information, see [Overview](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Shared GPU scheduling/Overview.md).

## Supported regions

-   Asia Pacific

    |Region|City|Region ID|
    |------|----|---------|
    |China \(Beijing\)|Beijing|cn-beijing|
    |China \(Zhangjiakou\)|Zhangjiakou|cn-zhangjiakou|
    |China \(Hohhot\)|Hohhot|cn-huhehaote|
    |China \(Ulanqab\)|Ulanqab|cn-wulanchabu|
    |China \(Hangzhou\)|Hangzhou|cn-hangzhou|
    |China \(Shanghai\)|Shanghai|cn-shanghai|
    |China \(Shenzhen\)|Shenzhen|cn-shenzhen|
    |China \(Heyuan\)|Heyuan|cn-heyuan|
    |China \(Chengdu\)|Chengdu|cn-chengdu|
    |China \(Hong Kong\)|Hong Kong|cn-hongkong|
    |Japan \(Tokyo\)|Tokyo|ap-northeast-1|
    |Singapore \(Singapore\)|Singapore|ap-southeast-1|
    |Australia \(Sydney\)|Sydney|ap-southeast-2|
    |Malaysia \(Kuala Lumpur\)|Kuala Lumpur|ap-southeast-3|
    |Indonesia \(Jakarta\)|Jakarta|ap-southeast-5|

-   Europe & Americas

    |Region|City|Region ID|
    |------|----|---------|
    |US \(Silicon Valley\)|Silicon Valley|us-west-1|
    |US \(Virginia\)|Virginia|us-east-1|
    |UK \(London\)|London|eu-west-1|
    |Germany \(Frankfurt\)|Frankfurt|eu-central-1|

-   India

    |Region|City|Region ID|
    |------|----|---------|
    |India \(Mumbai\)|Mumbai|ap-south-1|


