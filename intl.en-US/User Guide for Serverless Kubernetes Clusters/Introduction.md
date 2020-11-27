---
keyword: [ASK, serverless Kubernetes, overview, comparison between ASK and ACK]
---

# Introduction

This topic provides an overview of serverless Kubernetes \(ASK\) clusters, including their benefits, pricing, scenarios, and comparisons with clusters of Container Service for Kubernetes \(ACK\). This allows you to quickly get started with ASK clusters.

## Overview

ASK clusters allow you to deploy containerized applications without the hassle of purchasing, operations and maintenance \(O&M\), and capacity planning of nodes. You are charged based on the CPU and memory resources that you configure for your applications. ASK clusters provide optimized compatibility with Kubernetes and provides an easy method to get started with Kubernetes. You can focus on the design and development of your applications instead of the underlying infrastructure.

Each pod in an ASK cluster runs in a secure and isolated container runtime built on an elastic container instance \(ECI\). The underlying compute resources of different ECIs are completely isolated by lightweight virtual sandboxes. ECIs do not affect each other.

## Benefits

-   **Easy-to-use**: You can deploy an application in an ASK cluster within a few seconds. You can focus on the design and development of your applications instead of node management.
-   **Quick scaling**: You can effortlessly scale out resources based on your workload requirements without the hassle of node capacity planning.
-   **Secure isolation**: Pods are created based on ECIs. Pods of different applications are isolated from each other to prevent mutual interference.
-   **Cost-effective**: Pods are created based on your business requirements. You are charged based on resource usage. Idle resources are not charged. The serverless architecture reduces operational costs.
-   **Compatibility with Kubernetes**: ASK supports native Kubernetes resources such as Services, Ingresses, and Helm charts. This allows you to seamlessly migrate Kubernetes applications.
-   **Integration and interconnection**: Applications in ASK clusters can be seamlessly integrated with basic services that are provided by Alibaba Cloud. ASK clusters allow applications to communicate with existing applications and databases in the virtual private cloud \(VPC\) where the cluster is deployed. This also applies to other applications that run on virtual nodes of the cluster.

## Pricing

In ASK clusters, fees are charged based on pods instead of nodes. Pods are charged based on the pricing of ECI. For more information, see [ECI pricing overview]().

Alibaba Cloud services used by ASK clusters are also charged, such as Server Load Balancer \(SLB\) and PrivateZone. For more information, see the related pricing pages.

## Comparison between ASK and ACK

![Comparison between ASK and ACK](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3197297951/p10232.png)

## Scenario

-   **Application management**

    ASK clusters save you the hassle of purchasing, operations and maintenance \(O&M\), and capacity planning of nodes. This significantly reduces the costs of infrastructure management and O&M.

-   **Dynamic scaling**

    For workloads that have periodic traffic patterns, such as online education and e-commence applications, ASK clusters support dynamic scaling to significantly reduce computing costs and idle resources, and smoothly handle sudden traffic spikes.

-   **Data computing**

    To meet computing requirements for applications such as Spark, ASK clusters can start a large number of pods in a short period of time to support task processing. When the task is terminated, these pods are automatically released to stop billing. This significantly reduces the overall computing costs.

-   **CI/CD**

    You can use ASK clusters to implement continuous integration \(CI\) environments with tools such as Jenkins or GitLab-Runner. You can set up an application delivery pipeline that includes stages such as source code compilation, image build and push, and application deployment. The continuous integration tasks are isolated from one another for enhanced security. You do not need to maintain fixed resource pools. This reduces computing costs.

-   **CronJobs**

    You can run CronJobs in ASK clusters. Billing automatically stops when the related jobs are terminated. You do not need to maintain fixed resource pools. This avoids resource waste.

-   **Test environment**

    ASK clusters provide an out-of-the-box method for you to use and manage resources. You can quickly create and delete pods based on your business requirements at a low cost.


