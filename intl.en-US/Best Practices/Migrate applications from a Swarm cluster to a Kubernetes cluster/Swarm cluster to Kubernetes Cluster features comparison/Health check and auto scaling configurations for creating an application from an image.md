# Health check and auto scaling configurations for creating an application from an image

This topic describes the differences in health check and auto scaling configurations when you create an application from an image in a Container Service for Swarm cluster and in a Container Service for Kubernetes \(ACK\) cluster.

## Create an application from an image

The interfaces for creating an application from an image in a Swarm cluster and in an ACK cluster are quite different.

-   For more information about how to create an application from an image in a Swarm cluster, see [Create an application](/intl.en-US/User Guide/Applications/Create an application.md).
-   For more information about how to create an application from an image in an ACK cluster, see [Deploy a stateless application from an image](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Deploy a stateless application from an image.md).

## Health check

-   Swarm cluster

    Health checks are implemented by using labels.

-   ACK cluster

    You can configure health check settings on the Container wizard page. You can configure **liveness** and **readiness** checks.

    ![Container](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7546858951/p35542.png)

    ![Readiness check](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7546858951/p35543.png)


## Auto scaling

-   Swarm cluster

    Supports auto scaling based on the CPU and memory usage.

-   ACK cluster

    On the **Advanced** wizard page for creating an application from an image, you can enable horizontal scaling for pods based on the CPU and memory usage.

    ![Horizontal scaling](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8546858951/p35544.png)


