# Use ECI in Serverless Kubernetes

This topic describes how to use Elastic Container Instance \(ECI\) in Serverless Kubernetes. Serverless Kubernetes integrates Kubernetes clusters with Alibaba Cloud services, such as ECI. You can quickly create a serverless Kubernetes cluster and use ECI in the cluster.

## Serverless Kubernetes overview

ECI is compatible with Kubernetes. Based on ECI, Alibaba Cloud provides the Serverless Kubernetes service for you to create Kubernetes clusters that are maintenance-free and fully managed.

![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3961979851/p105059.png)

Serverless Kubernetes is a serverless platform that is optimized for running containers. This platform provides powerful capabilities for managing Kubernetes clusters and handling workloads, such as deployments, StatefulSets, jobs, and cron jobs. This platform allows you to abstract the architecture of applications and components and eliminates the need of server management, such as server creation, infrastructure management, O&M, upgrade, and capacity planning. You can focus on applications, rather than the underlying resources, and only pay for the resources used by the applications. Serverless Kubernetes automatically scales in and out resources based on application types, and manages resources in a finer-grained way.

Serverless Kubernetes has the following benefits:

-   Easy to use: You can create a serverless Kubernetes cluster within 1.5s and deploy an application in the cluster within 30s. You do not need to manage the Kubernetes cluster.
-   Integration with ecosystems: You can use the native API operations of Kubernetes to benefit from the Kubernetes ecosystem. Serverless Kubernetes is seamlessly integrated with the Alibaba Cloud infrastructure and allows the communications between Kubernetes clusters and the existing applications in your Virtual Private Clouds \(VPCs\).
-   Security isolation: Serverless Kubernetes provides powerful capabilities for strong pod isolation based on the elastic computing architecture.
-   Billed in the pay-as-you-go mode: You are charged based on the CPU and memory used by pods. You can configure auto scaling policies for Serverless Kubernetes to automatically scale resources.

## Scenarios

With the high portability and flexibility of containers and high scalability and isolation of the elastic computing architecture of Alibaba Cloud, Serverless Kubernetes can be used in a variety of scenarios. Serverless Kubernetes is an agile service that can be used in deployment of web applications and back-end services of mobile applications, multimedia processing, data processing, and continuous integration. It is especially suitable for processing batch jobs and burst traffic.

![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3961979851/p105063.png)

## Get started with ECI and Serverless Kubernetes

For more information about how to use ECI in Serverless Kubernetes, see [Get started with ECI]() or [Serverless Kubernetes Quick Start](https://yq.aliyun.com/articles/591115).

For more information about Serverless Kubernetes, see [Overview](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Overview.md).

