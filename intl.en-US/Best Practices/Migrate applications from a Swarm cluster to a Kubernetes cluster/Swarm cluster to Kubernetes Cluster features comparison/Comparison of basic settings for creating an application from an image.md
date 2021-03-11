# Comparison of basic settings for creating an application from an image

This topic describes the differences in the basic settings when you create an application from an image in a cluster of Container Service for Swarm and in a cluster of Container Service for Kubernetes \(ACK\).

## Create an application from an image

The interfaces for creating an application from an image in a Swarm cluster and in an ACK cluster are quite different.

-   For more information about how to create an application from an image in a Swarm cluster, see [Create an application](/intl.en-US/User Guide/Applications/Create an application.md).
-   For more information about how to create an application from an image in an ACK cluster, see [Deploy a stateless application from an image](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Deploy a stateless application from an image.md).

## Basic settings

Swarm cluster

On the Basic Information wizard page, you must specify the following information: application name, application version, cluster for deployment, deployment model, and description.

![Swarm cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1546858951/p35310.png)

ACK cluster

On the Basic Information wizard page, you must specify the following information that is required only by an ACK cluster: namespace for deployment, number of replicas, and application type.

Namespace is a Kubernetes-specific concept. Namespaces are used to isolate resources such as CPU resources within a Kubernetes cluster. You can also use namespaces to distinguish different environments, such as staging environments and development environments. If you want to isolate resources among production environments, we recommend that you create a cluster for each production environment. For more information about Kubernetes namespaces, see [Terms](/intl.en-US/Product Introduction/Terms.md).

![ACK cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1546858951/p35309.png)

## General

In the General section, you must specify the image name and image version.

Swarm cluster

You can set **Network Mode** to **Default** or **host**.

![Swarm cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1546858951/p35331.png)

ACK cluster

-   The network mode is automatically selected when you create an ACK cluster. You can select **Flannel** or **Terway** as the network plug-in. For more information, see [t64408.md\#section\_k1z\_f1n\_lmh](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Container network/Work with Terway.md).
-   The Required Resources field specifies the amount of CPU and memory resources that are required by a container. The Resource Limit field specifies the maximum resource amount that can be allocated to a container. These fields are similar to the **CPU Limit** and **Memory Limit** fields in the Container section when you create an application from an image in a Swarm cluster.

![ACK cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2546858951/p35332.png)

