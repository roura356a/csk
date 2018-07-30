# Overview {#concept_jlf_y4f_vdb .concept}

Kubernetes is a popular open-source container orchestration technology.  To allow you to use Kubernetes to manage container applications in Alibaba Cloud,  Alibaba Cloud Container Service provides support for Kubernetes clusters.

You can create a safe and high-availability Kubernetes cluster  in the Container Service console. The Kubernetes cluster integrates with the virtualization, storage, network, and security capabilities of Alibaba Cloud to provide scalable, high-performance container application management, simplify cluster creation and expansion, and focus on the development and management of containerized applications.

Kubernetes supports the deployment, expansion, and management of containerized applications,  and provides the following features:

-   Elastic expansion and self-reparation.
-   Service discovery and server load balancing.
-   Service release and rollback.
-   Secret and configuration management.

## Limits {#section_pnh_cpf_vdb .section}

-   Currently, Kubernetes clusters only support Linux containers. The support for Kubernetes Windows containers is in the works.
-   Currently, Kubernetes clusters only support the network type Virtual Private Cloud \(VPC\).  You can select to create a VPC or use an existing VPC when creating the Kubernetes cluster .
-   Currently, all resources \(for example, the Elastic Compute Service \(ECS\) instances and Server Load Balancer instances\) created in the Kubernetes cluster are Pay-As-You-Go resources.  You can change the Pay-As-You-Go instances to monthly or yearly subscription instances in the console. Monthly or yearly subscription resources will be supported in the future.

## Related open-source projects {#section_dk3_1pf_vdb .section}

-   Alibaba Cloud Kubernetes Cloud Provider: [https://github.com/AliyunContainerService/kubernetes](https://github.com/AliyunContainerService/kubernetes).
-   Alibaba Cloud VPC network drive for Flannel: [https://github.com/coreos/flannel/blob/master/Documentation/alicloud-vpc-backend.md](https://github.com/coreos/flannel/blob/master/Documentation/alicloud-vpc-backend.md).

If you have any questions or suggestions regarding a specific project, you are welcome to raise an issue or pull a request in the community.

