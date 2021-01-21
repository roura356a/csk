ASK使用ECI 
=============================

本文介绍如何通过阿里云容器服务Serverless Kubernetes（ASK）使用ECI。ASK已经帮您整合好Kubernetes集群、阿里云各类服务以及ECI，您可以直接通过一键创建集群来直接使用ECI。

ASK简介 
--------------------------

阿里云容器服务Serverless Kubernetes（ASK）是阿里云基于弹性计算架构推出的无服务器Kubernetes容器服务，您无需购买节点，无需对集群进行节点维护和容量规划，即可直接部署容器应用，并且只需要为应用配置的CPU和内存资源量进行按需付费。

ASK提供了完善的Kubernetes兼容能力，您可以直接使用 Kubernetes API或者命令行直接管理容器应用。同时，ASK集成阿里云各类服务，可以帮助您简化Kubernetes的开发，专注于应用构建而非基础架构运维。

如下图所示，在ASK集群中，您无需手动部署虚拟节点，可以直接创建Pod。Pod基于ECI运行在安全隔离的容器运行环境中，每个Pod对应一个ECI实例。![ASK ](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9817728061/p203175.png)

更多信息，请参见[ASK产品概述](/intl.zh-CN/Serverless Kubernetes集群用户指南/ASK概述.md)。

使用流程 
-------------------------

您可以在容器服务管理控制台创建ASK集群，并在集群下直接部署应用。
**说明**

部署ASK集群生成的Pod，与ECI实例一一对应，您也可以在ECI控制台查看相关信息。

相关使用流程如下：

1. [创建Serverless Kubernetes集群](/intl.zh-CN/Serverless Kubernetes集群用户指南/集群管理/创建Serverless Kubernetes集群.md)

   

2. [使用镜像创建应用](/intl.zh-CN/Serverless Kubernetes集群用户指南/应用管理/使用镜像创建应用.md)

   

3. [创建服务](/intl.zh-CN/Serverless Kubernetes集群用户指南/应用管理/创建服务.md)

   

4. [查看容器](/intl.zh-CN/Serverless Kubernetes集群用户指南/应用管理/查看容器.md)

   



