# Scenarios

This topic describes common scenarios of Container Service for Kubernetes \(ACK\).

## DevOps and continuous delivery

Optimal continuous delivery pipeline

ACK is integrated with Jenkins to automate the DevOps pipeline that covers code submission to application deployments. The pipeline ensures that code is submitted for deployment only after the code passes automated testing. The pipeline is a new and efficient method. We recommend that you do not use traditional delivery models that include complex deployments and slow iterations.

Benefits

-   DevOps pipeline automation

    Automates the DevOps pipeline, from code updates to code builds, image builds, and application deployments.

-   Environment consistency

    Allows you to deliver code and runtime environments based on the same architecture.

-   Continuous feedback

    Provides immediate feedback on each integration or delivery.


Related services

Elastic Compute Service \(ECS\) and ACK

![DevOps](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4986258951/p7271.png)

## Microservice architecture

Agile development and deployment to accelerate the development of business models

Your workload in the production environment is divided into multiple microservice applications. These microservice applications are managed by Alibaba Cloud image repositories. Alibaba Cloud can schedule, orchestrate, deploy, and implement the canary releases of microservice applications. Therefore, you can focus on feature updates.

Benefits

-   Load balancing and service discovery

    Forwards and binds Layer 4 and Layer 7 requests to backend containers.

-   Multiple scheduling and disaster recovery policies

    Supports different levels of affinity scheduling policies, and cross-zone high availability and disaster recovery.

-   Microservice monitoring and auto scaling

    Supports microservice and container monitoring, and microservice auto scaling.


Related services

ECS, ApsaraDB RDS, and Object Storage Service \(OSS\)

![SLB](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4986258951/p7273.png)

## Hybrid cloud architecture

Unified O&M of cloud resources

You can manage both cloud and on-premises resources in the ACK console. Container Service does not rely on specific infrastructure. Therefore, you can use the same images and orchestration templates to deploy applications both in the cloud and on the premises.

Benefits

-   Application scaling in the cloud

    During peak hours, ACK can scale up applications in the cloud and forward traffic to the scaled-up resources.

-   Disaster recovery in the cloud

    Business systems can be deployed on the premises for service provisioning and in the cloud for disaster recovery.

-   On-premises development and testing

    Applications that are developed and tested on the premises can be seamlessly released to the cloud.


Related services

ECS, Virtual Private Cloud \(VPC\), and Express Connect

![ACK](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5986258951/p7274.png)

## Auto scaling architecture

Traffic-based scalability

ACK supports auto scaling of cloud resources for your workloads based on network traffic. This prevents system failures that are caused by traffic spikes and eliminates idle resources during off-peak hours.

Benefits

-   Quick response

    A scale-out event can be triggered within a few seconds when network traffic reaches the scale-out threshold.

-   Auto scaling

    The scaling process is automated. This eliminates human errors.

-   Cost efficiency

    Containers are automatically scaled in when network traffic decreases. This allows you to maximize resource usage.


Related services

ECS and Cloud Monitor

![Cloud Monitor](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5986258951/p7275.png)

