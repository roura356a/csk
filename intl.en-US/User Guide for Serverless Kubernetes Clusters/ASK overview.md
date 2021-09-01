---
keyword: [ASK, serverless Kubernetes, overview, comparison between ASK and ACK]
---

# ASK overview

This topic provides an overview of serverless Kubernetes \(ASK\) clusters, including the benefits, pricing, and use scenarios. This topic also compares ASK clusters with Container Service for Kubernetes \(ACK\) clusters. This helps you quickly get started with ASK clusters.

## Overview

ASK is a serverless Kubernetes service provided by Alibaba Cloud. ASK clusters allow you to deploy containerized applications without the need to purchase nodes. You do not need to perform capacity planning or operations and maintenance \(O&M\) tasks on the nodes. You are charged based on the CPU and memory resources that are configured for your applications. ASK clusters are compatible with Kubernetes and make it easy to get started with Kubernetes. You can focus on the design and development of your applications instead of the underlying infrastructure.

Each pod in an ASK cluster runs in a secure and isolated container runtime that is built on an elastic container instance. The underlying computing resources of each elastic container instance are isolated by lightweight virtual sandboxes. Elastic container instances do not affect each other.

## Benefits

-   **O&M-free**: You can deploy an application in an ASK cluster within a few seconds. You can focus on application development without the need to manage nodes.
-   **Auto scaling**: You do not need to perform capacity planning for the cluster. ASK automatically scales resources based on your workload requirements.
-   **Kubernetes compatibility**: ASK supports Kubernetes-native resources such as Services, Ingresses, and Helm charts. This allows you to seamlessly migrate Kubernetes applications.
-   **Secure isolation**: Pods are created based on elastic container instances. Pods of different applications are isolated from each other to prevent mutual interference.
-   **Cost-effectiveness**: Pods are created based on your business requirements. You are charged based on the resources used by your applications. You are not charged for idle resources. In addition, the serverless architecture helps reduce O&M costs.
-   **Integration and interconnection**: Containerized applications in ASK clusters support seamless integration with Alibaba Cloud fundamentals. These applications can communicate with existing applications and databases in the virtual private cloud \(VPC\) where the cluster is deployed. The containerized applications can also communicate with VM-based applications.

## Pricing

When you use ASK clusters, you are charged for pods instead of nodes. The fees of pods are calculated based on the pricing of Elastic Container Instance. For more information, see [Elastic Container Instance pricing overview]().

You are also charged for other resources used in the clusters, such as Server Load Balancer \(SLB\) and PrivateZone. For more information about the pricing, see the following references:

-   [ALB billing](/intl.en-US/Application Load Balancer/ALB billing.md)
-   [PrivateZone pricing](/intl.en-US/Announcements & Updates/[New] Notice on the official billing of the Resolver feature.md)

## Comparison between ASK and ACK

![Comparison between ASK and ACK](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/3197297951/p10232.png)

## Use scenarios

-   **Application management**

    In ASK clusters, you do not need to manage or maintain nodes, or perform capacity planning. This reduces the costs of infrastructure management and O&M.

-   **Dynamic scaling**

    For workloads that have periodic traffic patterns, such as online education and e-commence applications, ASK clusters can automatically scale resources based on workload requirements. This prevents resource waste, reduces computing costs, and ensures a smooth user experience when traffic spikes occur.

-   **Data computing**

    To meet computing requirements of applications such as Spark, ASK clusters can start a large number of pods within a short period of time to process tasks. When the tasks are terminated, the pods are automatically released to stop billing. This dramatically reduces the overall computing costs. For more information, see [Use ASK to create Spark tasks](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Serverless cluster best practices/Use ASK to create Spark tasks.md).

-   **CI/CD**

    You can use ASK clusters to build continuous integration \(CI\) environments by using tools such as Jenkins or GitLab-Runner. You can set up an application delivery pipeline that covers stages such as source code compilation, image building and pushing, and application deployment. The continuous integration tasks are isolated from each other for enhanced security. You do not need to maintain specific resource pools. This reduces computing costs. For more information, see [Elastic and cost-effective CI/CD based on ASK](/intl.en-US/Best Practices/DevOps/Elastic and cost-effective CI/CD based on ASK.md).

-   **CronJobs**

    You can run CronJobs in ASK clusters. Billing automatically stops when the jobs are terminated. You do not need to maintain specific resource pools. This avoids resource waste.


