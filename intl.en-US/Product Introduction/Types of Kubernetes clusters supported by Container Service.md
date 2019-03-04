# Types of Kubernetes clusters supported by Container Service {#concept_om5_1wl_5gb .concept}

This topic describes three types of Kubernetes clusters \(dedicated, managed, and serverless\) in terms of their features, the resources that you pay for, their applicable scenarios, the user features, and the creation procedures.

Alibaba Cloud Container Service for Kubernetes \(ACK for short\) provides high-performance and scalable cloud native application management capabilities, and supports full life cycle management of enterprise-level Kubernetes containerized applications.

## Features {#section_pwx_mwl_5gb .section}

-   Dedicated Kubernetes cluster

    You must create three Master nodes and one or multiple Worker nodes for the cluster. In addition, you need to plan, maintain, and upgrade the cluster as needed. With such a Kubernetes cluster, you can control cluster infrastructures in a more fine-grained manner.

-   Managed Kubernetes cluster

    You only need to create Worker nodes for the cluster, and Alibaba Cloud Container Service for Kubernetes creates and manages Master nodes for the cluster. This type of Kubernetes cluster is easy to use, low-cost, and highly available. You can focus on the services supported by the cluster without needing to operate and maintain the Kubernetes cluster Master nodes.

-   Serverless Kubernetes cluster

    You do not need to create or manage any Master nodes or Worker nodes for the cluster. You can directly use the Container Service console or the command line interface to set container resources, specify container images for applications, set methods to provide services, and start applications.


## Resources that you pay for {#section_wps_vwl_5gb .section}

-   Dedicated Kubernetes cluster

    The Kubernetes cluster can be used free of charge. You are charged only for Master node and Worker node resources.

-   Managed Kubernetes cluster

    The Kubernetes cluster can be used free of charge. You are charged only for Worker node resources.

-   Serverless Kubernetes cluster

    You are charged for the resources used by container instances. The amount of used resources is measured according to resource usage duration \(in seconds\).


## Applicable scenarios {#section_zfk_1xl_5gb .section}

-   Dedicated Kubernetes cluster

    Applies to all scenarios.

-   Managed Kubernetes cluster

    Applies to all scenarios.

-   Serverless Kubernetes cluster

    Applies to batch tasks, burst expansion, and CI/CD testing.


## User features {#section_vfk_2xl_5gb .section}

-   Dedicated Kubernetes cluster users
    -   Have little to no cost constraints regarding their requirements.
    -   Are capable of operating and maintaining Kubernetes clusters.
    -   Have a deep understanding of Kubernetes.
    -   Have clear cluster resource plans.
    -   Require tailored Master nodes.
    -   Require completely self-managed Kubernetes clusters.
-   Managed Kubernetes cluster users
    -   Have some cost constraints regarding their requirements.
    -   Want to focus on application deployment and operation without needing to worry about cluster management.
    -   Have a moderate understanding of Kubernetes.
    -   Require lightweight Kubernetes cluster O&M.
    -   Do not want to worry about Master node management.
-   Serverless Kubernetes cluster users
    -   Need to process batch tasks.
    -   Need to process bursts of service expansion.
    -   Desire out-of-the-box functionality.
    -   Have zero O&M requirements.
    -   Prefer to pay for resources as they are used \(Pay-As-You-Go billing method\).
    -   Do not want to worry about infrastructure setup.

## Creation procedures {#section_t1y_2xl_5gb .section}

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/125425/155169115438875_en-US.png)

