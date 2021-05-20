---
keyword: [Container Registry, features, scenarios]
---

# Container Registry

You can use Container Registry when you deploy applications. Container Registry can automatically build images and allows applications to automatically pull images. In addition, Container Registry improves the speed of image pulling and reduces the deployment time of applications.

Container Registry Enterprise Edition is an enterprise-class platform designed to manage the lifecycle of cloud native application artifacts, including container images, Helm charts, and Open Container Initiative \(OCI\) artifacts. Container Registry Enterprise Edition efficiently distributes large-scale application artifacts across multiple regions in different scenarios. Container Registry Enterprise Edition seamlessly integrates with Container Service for Kubernetes \(ACK\), which simplifies the application delivery for enterprises.

For more information about how to get started with Container Registry, see [Create a Container Registry Enterprise Edition instance]() and [Use a Container Registry Enterprise Edition instance to push and pull images]().

## Features

-   OCI artifact management: Container Registry Enterprise Edition can manage multiple types of OCI artifacts, such as container images for multiple architectures including Linux, Windows, and ARM, and charts of Helm v2 and Helm v3.
-   Multi-dimensional security protection: Container Registry Enterprise Edition ensures storage and content security by storing encrypted cloud native application artifacts, scanning container images for vulnerabilities, and generating vulnerability reports in multiple dimensions. Container Registry Enterprise Edition ensures secure access by providing network access control and fine-grained operation audit for container images and Helm charts.
-   Accelerated application distribution: Container Registry Enterprise Edition can synchronize container images among different regions around the world to improve the distribution efficiency. Container Registry Enterprise Edition supports P2P image distribution to accelerate application deployment and scaling.
-   Efficient and secure cloud native application delivery: Container Registry Enterprise Edition allows you to create cloud native application delivery chains that are observable, traceable, and configurable. Based on delivery chains and blocking rules, Container Registry Enterprise Edition can automatically deliver applications all over the world upon source code changes in multiple scenarios. This improves the efficiency and security of cloud native application delivery.

## Scenarios

-   Global business deployment

    If your business is deployed around the world but you perform R&D and iteration activities in mainland China, you may not directly pull container images from regions in mainland China due to factors such as network instability. You can purchase Container Registry Enterprise Edition in global regions and configure rules to automatically synchronize container images. The automatic synchronization allows you to pull container images from the nearest region and implement cross-region disaster recovery when your business expands to different global regions.

-   Large-scale business deployment

    If your business is large-scale and encounters bursts or iterations, you may not pull container images in time to cope with traffic peaks. Container Registry Enterprise Edition provides multiple instance types that allow you to simultaneously pull container images for all nodes in clusters with different scales. You can select an instance type based on the number of nodes in your cluster. Container Registry Enterprise Edition provides multiple methods to accelerate large-scale image distribution. This helps you ensure efficient container scaling and deployment.

-   Efficient DevSecOps environment building based on containers

    If your business is deployed on platforms such as ACK, Alibaba Cloud Serverless Kubernetes, Enterprise Distributed Application Service \(EDAS\), and Elastic Container Instance \(ECI\), you must ensure security and efficiency of your business delivery chain. Container Registry Enterprise Edition allows you to create cloud native application delivery chains that are observable, traceable, and configurable. Based on the delivery chains, application artifacts can be automatically created, scanned for vulnerabilities, and signed upon source code changes. If risks are detected based on blocking rules, Container Registry Enterprise Edition automatically stops follow-up steps in the delivery chains. Application artifacts with no risks can be automatically distributed in different global regions. This allows you to deploy cloud native applications in different scenarios. Based on cloud native application delivery chains, you can include security into the business delivery process and upgrade DevOps to DevSecOps. In this way, you can ensure a more secure and efficient delivery of cloud native application artifacts.

-   On-premises data migration to the cloud

    Migrating cloud native application artifacts to the cloud is the top priority in scenarios where you migrate on-premises data to the cloud. Container Registry Enterprise Edition allows you to efficiently import images from Object Storage Service \(OSS\) buckets to Container Registry Enterprise Edition instances. You can also use migration tools to smoothly migrate images from container image services of other service providers, such as Google, Azure, and AWS, to Container Registry Enterprise Edition.


