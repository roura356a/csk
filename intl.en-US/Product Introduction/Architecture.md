# Architecture

Container Service for Kubernetes is adapted and enhanced on the basis of native Kubernetes. This service simplifies cluster creation and scaling and integrates Alibaba Cloud capabilities of virtualization, storage, networking, and security, providing the optimal environment to run Kubernetes-based containerized applications in the cloud.

![architecture](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4986258951/p43547.png)

|Feature|Description|
|-------|-----------|
|Multiple types of Kubernetes clusters|Integrated with virtualization technologies of Alibaba Cloud, Container Service for Kubernetes supports Dedicated Kubernetes clusters, Managed Kubernetes clusters, and Serverless Kubernetes clusters. -   Dedicated Kubernetes cluster: ECS, Elastic GPU Service \(EGS\), and ECS Bare Metal \(EMB\) instances can all be used as cluster nodes. Instances support a wide range of plug-ins and can be flexibly configured to different specifications.
-   Managed Kubernetes cluster: Container Service for Kubernetes manages master nodes of a Managed Kubernetes clusters. You only need to create worker nodes. This type of Kubernetes cluster is easy to use with low cost and high availability. You can focus on the business without the need to manage the master nodes of the Kubernetes cluster.
-   Serverless Kubernetes cluster: Container Service for Kubernetes provides Serverless Kubernetes clusters that simplify underlying resource management, support flexible scaling, and reduce resource consumption. |
|Cluster management and control|Container Service for Kubernetes provides a variety of cluster management and control features, such as networking, storage, hybrid cluster management, horizontal scaling, and application extension.|
|Kubernetes management|Container Service for Kubernetes supports secure images and Helm, and is highly integrated with other Alibaba Cloud services, such as Resource Access Management \(RAM\), Key Management Service \(KMS\), Log Service, and CloudMonitor. Container Service for Kubernetes provides a secure and standard-compliant Kubernetes solution. It provides you with enhanced capabilities such as hybrid cloud, container security, continuous integration or continuous delivery \(CI/CD\), and DevOps.|
|Convenient and efficient use|Container Service for Kubernetes provides services through the web console, API, and SDK.|

