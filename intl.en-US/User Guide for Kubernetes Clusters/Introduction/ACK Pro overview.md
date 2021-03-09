---
keyword: [ACK Pro, professional managed Kubernetes clusters]
---

# ACK Pro overview

ACK Pro clusters are developed on top of managed Kubernetes clusters. ACK Pro clusters offer higher reliability and security in large-scale production environments for enterprise users. ACK Pro clusters are also covered by the service-level agreement \(SLA\) that supports compensation clauses.

ACK Pro clusters provide all benefits of managed Kubernetes clusters. For example, master nodes are also highly available and managed by Container Service for Kubernetes \(ACK\). In addition, ACK Pro clusters provide higher reliability, security, and schedulability. ACK Pro clusters are covered by terms of SLA for customer compensation. ACK Pro clusters are suitable for enterprises that require higher security and stability for large-scale business deployed in a production environment.

## Scenarios

-   Internet enterprises. These enterprises deploy their business on a large scale and require business management with high stability, security, and observability.
-   Big data computing enterprises. These enterprises deploy large-scale data computing services, high-performance data processing services, and other services with high elasticity. These services require clusters with high stability, high performance, and efficient computing capabilities.
-   Foreign enterprises who run their business in China. These enterprises hold the standard compensation SLA and security in high regard.
-   Financial enterprises. These enterprises require the standard compensation SLA.

## Features

-   Managed master nodes with higher reliability: The API server can automatically scale a cluster to a large number of nodes without service disruption. etcd is a reliable store for disaster recovery and data restoration. etcd uses cold backups and hot backups to ensure the availability of databases for ACK Pro clusters. Key metrics are collected for you to gain insights from control components. This allows you to predict potential risks on master node management.
-   Clusters with higher security: In the control plane, etcd uses encrypted disks by default. In the data plane, kms-plugin is installed to encrypt Kubernetes Secrets. ACK provides security management for this type of cluster. You can use security management of the professional edition to inspect running containers and enable auto repairing.
-   More intelligent pod scheduling capability: kube-scheduler is integrated to provide better pod scheduling capability. This allows you to schedule pods in bulk, set multiple scheduling algorithms, and schedule pods to NPU-accelerated nodes. This also enhances the pod scheduling capability in scenarios where large-scale data computing or high-performance data processing is required.
-   SLA guarantees: ACK Pro clusters are covered by the SLA that supports compensation clauses. A level of 99.95% uptime is guaranteed for the cluster API server.

## Pricing

For more information about the pricing of ACK Pro clusters, see [Billing](/intl.en-US/Pricing/Billing.md).

## Comparison

The following table compares ACK Pro clusters and ACK standard clusters.

**Note:** Managed ACK clusters are renamed ACK standard clusters.

|Category|Managed Kubernetes cluster|
|ACK Pro cluster|ACK standard cluster|
|--------|--------------------------|
|---------------|--------------------|
|Cluster size|Up to 5,000 nodes.|Up to 100 nodes for each newly created cluster. A maximum of 5,000 nodes can be deployed in an existing ACK standard cluster. Existing ACK standard clusters can be upgraded to ACK Pro clusters.|
|SLA|99.95% \(supports compensation\).|99.90% \(does not support compensation\).|
|API Server|-   Provides the auto scaling feature.
-   Monitors availability.

|Does not support features that are provided by ACK Pro clusters.|
|etcd|Supports high-frequency cold backups, high-frequency hot backups, and geo-disaster recovery.|Does not support features that are provided by ACK Pro clusters.|
|Kube-scheduler|-   Supports gang scheduling. For more information, see [Gang scheduling](/intl.en-US/User Guide for Kubernetes Clusters/Introduction/Resource scheduling/Gang scheduling.md).
-   Supports topology-aware CPU scheduling. For more information, see [Topology-aware CPU scheduling](/intl.en-US/User Guide for Kubernetes Clusters/Introduction/Resource scheduling/Topology-aware CPU scheduling.md).
-   Supports topology-aware GPU scheduling For more information, see [Topology-aware GPU scheduling](/intl.en-US/User Guide for Kubernetes Clusters/Introduction/GPU topology-aware scheduling/Overview.md).
-   Supports cGPU Professional Edition. For more information, see [Overview](/intl.en-US/User Guide for Kubernetes Clusters/Introduction/cGPU Professional Edition/Overview.md).

|Supports cGPU Basic Edition. For more information, see [Overview](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU management/Shared GPU scheduling/Overview.md).|
|Security management|Supports the professional edition and data encryption. For more information, see [Use KMS to encrypt Kubernetes secrets at rest in the etcd](/intl.en-US/User Guide for Kubernetes Clusters/Introduction/Use KMS to encrypt Kubernetes secrets at rest in the etcd.md).|Supports the basic edition.|
|Managed node pool|Supported. For more information, see [Overview](/intl.en-US/User Guide for Kubernetes Clusters/Introduction/Managed node pools/Overview.md).|Not supported.|

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


