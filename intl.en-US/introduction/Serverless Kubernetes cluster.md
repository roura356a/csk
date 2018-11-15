# Serverless Kubernetes cluster {#concept_f3m_dgc_5db .concept}

## Introduction {#section_r2q_2gc_5db .section}

Alibaba Cloud serverless Kubernetes allows you to quickly create Kubernetes container applications without having to manage and maintain clusters and servers. Based on to the amount of CPU and memory resources actually used by the applications, Pay-as-you-go is applied.  By using serverless Kubernetes, you can focus on designing and building applications, rather than managing the infrastructure on which your applications run. It is based on the Alibaba Cloud flexible computing infrastructure and is fully compatible with the Kubernetes API, combining the security, elasticity, and Kubernetes ecosystem of virtualized resources.

Alibaba Cloud Kubernetes service has two modes: one is the classic cluster approach, and the other is the serverless mode.

-   In the classic Kubernetes cluster mode, you can get more fine-grained control over cluster infrastructure and container applications. For example, select the host instance specification \(ECS/EGS/EBM\) and the operating system, and specify the Kubernetes version, custom Kubernetes attribute switch settings, use different network models, and more.  The Alibaba Cloud Kubernetes service is responsible for creating the underlying cloud resources for the cluster, upgrading the cluster and other automated operations. It monitors CPU, memory, and other resources in the cluster, and finds the server that best fits the container according to the resource requirements you specify. You need to plan, maintain, and upgrade the server cluster. You can also add or remove servers in the cluster manually or automatically by monitoring the resource usage of the application container.
-   In serverless mode, you do not need to create the underlying virtualization resource. You can launch the application directly by using the Kubernetes command to indicate application container image, CPU and memory requirements, and external service methods.

## Benefits {#section_py2_ggc_5db .section}

-   **Easy to use**: Second-level application deployment. No need to manage the Kubernetes cluster infrastructure for high availability and security.
-   **Compatibility**: You can deploy container applications by using the familiar Kubernetes command line and API that support Kubernetes typical application scenarios without modification. You can also easily migrate your applications to a running platform that supports Kubernetes, such as the Alibaba Cloud Kubernetes cluster.
-   **Secure isolation**: Based on the flexible computing infrastructure of the Alibaba Cloud, application containers are isolated from each other to prevent interference.
-   **On-demand capacity expansion**: You do not need to worry about horizontal expansion of cluster nodes. Based on application load, you can easily and flexibility to scale up the resources needed for your application.
-   **Connectivity**: Supports seamless integration of container applications with Alibaba Cloud basic services. Supports direct interaction with existing applications and databases in your Virtual Private Cloud \(VPC\); supports the connection of containers with virtual machine applications.

## Architecture {#section_u2q_2gc_5db .section}

The comparison architecture of serverless Kubernetes Container Service and Kubernetes Container Service is shown below.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6864/15422717491393_en-US.png)

## Scenarios {#section_w2q_2gc_5db .section}

Serverless Kubernetes has a wide range of application scenarios. The high portability and flexibility based on containers, and the high elasticity and isolation of Infrastructure as a Service \(IaaS\) provide a powerful base operating facility for serverless applications. Serverless applications include multimedia processing, captured data modification, IoT sensor data processing, streaming computing, chat robotics, batch computing, web applications, mobile application backend services, business logic processing, and continuous integration.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6864/15422717491394_en-US.png)

