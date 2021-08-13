---
keyword: [ASK, serverless Kubernetes, overview, comparison between ASK and ACK]
---

# ASK overview

This topic provides an overview of serverless Kubernetes \(ASK\) clusters, including the benefits, pricing, and use scenarios. This topic also compares ASK clusters with Container Service for Kubernetes \(ACK\) clusters. This helps you quickly get started with ASK clusters.

## Overview

ASK is a serverless Kubernetes container service provided by Alibaba Cloud. ASK clusters allow you to deploy containerized applications without the need to purchase nodes or plan capacity for nodes. In addition, you do not need to perform operations and maintenance \(O&M\) on nodes. You are charged based on the CPU and memory resources that are configured for your applications. ASK clusters are compatible with Kubernetes and provide easy access to Kubernetes. You can focus on the design and development of your applications instead of the underlying infrastructure.

Each pod in an ASK cluster runs in a secure and isolated container runtime that is built on an elastic container instance. The underlying compute resources of each elastic container instance are isolated by lightweight virtual sandboxes. Elastic container instances do not affect each other.

## Benefits

-   **Free of maintenance**: You can deploy an application in an ASK cluster within a few seconds. You can focus on application development without the need to manage nodes.
-   **Rapid elasticity**: You do not need to plan node capacity. ASK clusters automatically scale resources based on your workload requirements.
-   **Compatibility with Kubernetes**: ASK supports Kubernetes-native resources such as Services, Ingresses, and Helm charts. This allows you to seamlessly migrate Kubernetes applications.
-   **Secure isolation**: Pods are created based on elastic container instances. Pods of different applications are isolated from each other to prevent mutual interference.
-   **Cost-effectiveness**: Pods are created based on your business requirements. You are charged based on the resources used by your applications. You are not charged for idle resources. ASK clusters are developed on a serverless architecture, which helps reduce O&M costs.
-   **Integration and interconnection**: Applications in ASK clusters can be seamlessly integrated with basic services that are provided by Alibaba Cloud. The applications in an ASK cluster can communicate with applications and databases in the virtual private cloud \(VPC\) where the cluster is deployed. This also applies to other applications that run on virtual nodes of the cluster.

## Pricing

If you use ASK clusters, you are charged based on pods instead of nodes. Fees of pods are calculated based on the pricing of Elastic Container Instance. For more information, see [Elastic Container Instance pricing overview]().

You are also charged for other Alibaba Cloud services, such as Server Load Balancer \(SLB\) and PrivateZone, that are used by ASK clusters. For more information, see the related pricing pages.

## Comparison between ASK and ACK

![Comparison between ASK and ACK](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/3197297951/p10232.png)

## Use scenarios

-   **Application management**

    In ASK clusters, you do not need to manage and maintain nodes. In addition, you do not need to plan capacity for nodes. This reduces the costs of infrastructure management and O&M.

-   **Dynamic scaling**

    For workloads that have periodic traffic patterns, such as online education and e-commence applications, ASK clusters can automatically scale resources based on workload requirements. This way, the computing costs and idle resources are reduced, and traffic spikes can be handled in a more efficient manner.

-   **Data computing**

    To meet computing requirements for applications such as Spark, ASK clusters can start a large number of pods within a short period of time to process tasks. When task are terminated, ASK clusters automatically release pods to stop billing. This reduces the computing costs.

-   **CI/CD**

    You can use ASK clusters to build continuous integration \(CI\) environments by using tools such as Jenkins or GitLab-Runner. You can set up an application delivery pipeline that includes stages such as source code compilation, image build and push, and application deployment. The continuous integration tasks are isolated from each other for enhanced security. You do not need to maintain fixed resource pools. This reduces computing costs.

-   **CronJobs**

    You can run CronJobs in ASK clusters. Billing automatically stops when the related jobs are complete. You do not need to maintain specific resource pools. This avoids resource waste.


