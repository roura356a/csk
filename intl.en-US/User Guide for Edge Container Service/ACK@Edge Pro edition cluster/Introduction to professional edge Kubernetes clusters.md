---
keyword: professional edge Kubernetes clusters
---

# Introduction to professional edge Kubernetes clusters

Professional edge Kubernetes clusters offer higher reliability and security than standard edge Kubernetes clusters in large-scale production environments for enterprise users. In addition, professional edge Kubernetes clusters are covered by the service level agreement \(SLA\) that supports compensation clauses.

Professional edge Kubernetes clusters offer all benefits of standard edge Kubernetes clusters. For example, master nodes are highly available and managed by Container Service for Kubernetes \(ACK\). In addition, professional edge Kubernetes clusters provide higher reliability, security, and schedulability, and are covered by SLA terms for compensation. Professional edge Kubernetes clusters are suitable for enterprises that require higher security and stability for large-scale business deployed in a production environment.

## Scenarios

-   Internet enterprises. These enterprises deploy their business on a large scale and require business management with high stability, security, and observability.
-   Big data computing enterprises. These enterprises deploy large-scale data computing services, high-performance data processing services, and other services with high elasticity. These services require clusters with high stability, high performance, and efficient computing capabilities.
-   International enterprises that run their business in China. These enterprises prioritize security and services that provide SLAs with compensation clauses.
-   Financial enterprises. These enterprises require SLAs with compensation clauses.

## Features

-   Managed master nodes with high reliability: etcd is a reliable store for disaster recovery and data restoration. etcd uses cold backups and hot backups to ensure data availability for professional edge Kubernetes clusters. Key metrics are collected for you to gain insights from control components. This allows you to detect potential risks.
-   Clusters with higher security: By default, etcd uses encrypted disks in the control plane. In the data plane, kms-plugin is installed to encrypt Kubernetes Secrets. ACK provides security management for this type of cluster. The advanced security management feature allows you to inspect containers in the Running state and enable auto repairing.
-   More intelligent pod scheduling: kube-scheduler is integrated to provide better pod scheduling capability. This allows you to schedule pods in bulk, set multiple scheduling algorithms, and schedule pods to NPU-accelerated nodes. This also enhances the pod scheduling capability in scenarios where large-scale data computing or high-performance data processing is required.
-   SLA guarantees: Professional edge Kubernetes clusters are covered by the SLA that supports compensation clauses. A level of 99.95% uptime is guaranteed for the cluster API server.

## Pricing

For more information about the pricing of professional edge Kubernetes clusters, see [t1936963.md\#]().

## Comparison

The following table compares professional edge Kubernetes clusters with standard edge Kubernetes clusters.

|Category|Feature|ACK@Edge|
|Professional edge Kubernetes cluster|Standard edge Kubernetes cluster|
|--------|-------|--------|
|------------------------------------|--------------------------------|
|Cluster size|N/A|Up to 1,000 nodes.|Up to 100 nodes for each newly created cluster. A maximum of 1,000 nodes can be deployed in an existing standard edge Kubernetes cluster. Existing standard edge Kubernetes clusters can be upgraded to professional edge Kubernetes clusters.|
|SLA|N/A|99.95% \(supports compensation\).|99.90% \(does not support compensation\).|
|API Server|Custom parameter settings|![Supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232205.png)|![Not supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232208.png)|
|Availability monitoring|![Supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232205.png)|![Not supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232208.png)|
|ETCD|High-frequency cold backups, high-frequency hot backups, and geo-disaster recovery|![Supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232205.png)|![Not supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232208.png)|
|Observability metrics|![Supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232205.png)|![Not supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232208.png)|
|Kube-scheduler|Scheduling of logical volumes \(LVs\) by using Logical Volume Manager \(LVM\)|![Supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232205.png)|![Not supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232208.png)|
|[Gang scheduling](/intl.en-US/User Guide for Kubernetes Clusters/Scheduling/Workload scheduling/Gang scheduling.md)|![Supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232205.png)|![Not supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232208.png)|
|[Topology-aware CPU scheduling](/intl.en-US/User Guide for Kubernetes Clusters/Scheduling/Resource scheduling/Topology-aware CPU scheduling.md)|![Supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232205.png)|![Not supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232208.png)|
|[Topology-aware GPU scheduling](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/GPU Resource scheduling/GPU topology-aware scheduling/Overview.md)|![Supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232205.png)|![Not supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232208.png)|
|[cGPU Professional Edition](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/GPU Resource scheduling/cGPU Professional Edition/Overview.md)|![Supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232205.png)|![Not supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232208.png)Only cGPU Basic Edition is supported. For more information, see [Overview](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/GPU Resource scheduling/cGPU Professional Edition/Overview.md). |
|Security management|The advanced security management feature that supports data encryption. For more information, see [Use KMS to encrypt Kubernetes Secrets](/intl.en-US/User Guide for Edge Container Service/ACK@Edge Pro edition cluster/Use KMS to encrypt Kubernetes Secrets.md).|![Supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232205.png)|![Not supported.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2912887161/p232208.png)|

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


