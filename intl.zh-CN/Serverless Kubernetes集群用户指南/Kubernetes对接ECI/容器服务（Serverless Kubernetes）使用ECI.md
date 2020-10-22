# 容器服务（Serverless Kubernetes）使用ECI

本文介绍如何通过阿里云Serverless Kubernetes容器服务（ASK）使用ECI。ASK已经帮您整合好Kubernetes集群、阿里云各类服务以及ECI，您可以直接通过一键创建集群来使用ECI。

## Serverless Kubernetes简介

ECI兼容Kubernetes，阿里云提供了免运维和管理的Kubernetes容器服务Serverless Kubernetes。

![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/5711734951/p84970.png)

Serverless Kubernetes是针对容器优化的Serverless平台，享有Kubernetes的强大管理能力，并支持Workloads中的Deployment、StatefulSet、Job、CroJob，您可将应用架构和组件架构抽象出来，不用管理服务器的本身例如创建机器和管理基础设施、运维、升级和容量规划。只需关注产品应用的本身，按照应用消耗付费，而并非底层资源，根据应用类型自动弹性伸缩，提供更细粒度的资源供给和消耗。

Serverless Kubernetes优势主要有以下几个方面：

-   简单易用：1.5秒创建集群，30秒部署应用；无需管理Kubernetes集群。
-   生态融合：可使用Kubernetes原生API，享受Kubernetes生态；与阿里云基础设施无缝集成；支持和用户VPC的应用网络互联互通。
-   安全隔离：基于弹性计算基础架构，提供POD的强隔离。
-   按需计费：按POD的CPU和内存计费；可配置弹性伸缩策略。

## 使用场景

Serverless Kubernetes 结合容器的高移植性和高灵活性与阿里云弹性计算提供的弹性调度和隔离性，其应用场景非常广泛，可敏捷便利部署于包括 web 应用、移动应用后端服务、多媒体处理、数据处理、持续集成等多种场景，非常适合批量任务和突发工作。 ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/5711734951/p84972.png)

## 快速入门

通过Serverless Kubernetes创建ECI可以参见[使用流程]()或者[Serverless Kubernetes 快速入门指南](https://yq.aliyun.com/articles/591115)。

完整的Serverless Kubernetes使用手册，可以参见[概述](/intl.zh-CN/Serverless Kubernetes集群用户指南/产品概述.md)。

