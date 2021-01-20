---
keyword: [ACK, hybrid cloud]
---

# Overview

This topic describes the background and features of the multi-cloud and hybrid cloud solution provided by Container Service for Kubernetes \(ACK\).

## Background

Cloud hosting is the trend of the future. However, some enterprise customers opt for multi-cloud or hybrid cloud solutions out of concern about data sovereignty and security. The differences in infrastructure capabilities and security architectures among different cloud environments can lead to barriers between the enterprise IT architecture and operations and maintenance \(O&M\) systems. This increases the complexity of multi-cloud or hybrid cloud implementations and O&M costs.

In the cloud-native era, Kubernetes-led technologies shield the differences between infrastructures and usher in the arrival of application-centric, multi-cloud and hybrid cloud architectures. This facilicates application lifecycle management and resource scheduling in a unified method in multi-cloud and hybrid cloud environments.

## Features

![Architecture](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8365359951/p90913.png)

The multi-cloud and hybrid cloud solution built on ACK and Alibaba Cloud Service Mesh \(ASM\) provides the following features:

-   Unified cluster management

    External Kubernetes clusters deployed in data centers or other clouds can be registered to the ACK console for unified management. The ACK console supports features such as security management, application management and monitoring, and logging in a unified method.

-   Unified scheduling and auto scaling

    Supports auto scaling based on a unified resource scheduling framework. This allows customers to make full use of computing resources and enable auto scaling to handle unexpected traffic spikes.

-   Unified service governance

    Supports nearest access, workload failover, and canary release based on ASM. This enables applications deployed among multiple clusters and across regions to support scenarios such as cloud disaster recovery and active geo-redundancy.


