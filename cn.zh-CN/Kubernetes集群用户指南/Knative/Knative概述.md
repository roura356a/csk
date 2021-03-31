# Knative概述

Knative是一款基于Kubernetes的Serverless框架。其目标是制定云原生、跨平台的Serverless编排标准。Knative通过整合容器构建（或者函数）、工作负载管理（动态扩缩）以及事件模型这三者来实现的这一Serverless标准。

## Knative体系架构

在Knative体系架构下各个角色的协作关系。

![Knative体系架构](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2373001061/p48848.png)

-   Developers

    Serverless服务的开发人员可以直接使用原生的Kubernetes API基于Knative部署Serverless服务。

-   Contributors

    主要是指社区的贡献者。

-   Operators

    Knative可以被集成到支持的环境中，例如，云厂商，或者企业内部。目前Knative是基于Kubernetes来实现的，有Kubernetes的地方就可以部署Knative。

-   Users

    终端用户通过Istio网关访问服务，或者通过事件系统触发Knative中的Serverless服务。


## Knative核心组件

作为一个通用的Serverless框架，Knative由三个核心组件组成：

-   Tekton：提供从源码到镜像的通用构建能力。

    Tekton组件主要负责从代码仓库获取源码并编译成镜像和推送到镜像仓库。并且所有这些操作都是在Kubernetes Pod中进行的。

-   Eventing：提供了事件的接入、触发等一整套事件管理的能力。

    Eventing组件针对Serverless事件驱动模式做了一套完整的设计。包括外部事件源的接入、事件注册和订阅、以及对事件的过滤等功能。事件模型可以有效的解耦生产者和消费者的依赖关系。生产者可以在消费者启动之前产生事件，消费者也可以在生产者启动之前监听事件。

-   Serving：管理Serverless工作负载，可以和事件很好的结合并且提供了基于请求驱动的自动扩缩的能力，而且在没有服务需要处理的时候可以缩容到零个实例。

    Serving组件的职责是管理工作负载以对外提供服务。Knative Serving组件最重要的特性就是自动伸缩的能力，目前伸缩边界无限制。Serving还具有灰度发布能力。


## Knative add-on组件

Knative支持第三方add-on组件。当前支持GitHub add-on组件，用于提供GitHub事件源支持。

## Knative安装部署

标准托管Kubernetes 、标准专有Kubernetes以及Serverless Kubernetes （ASK）集群支持部署Knative。部署方式如下：

-   在标准托管Kubernetes集群和标准专有Kubernetes集群中安装部署Knative，详细介绍请参见[一键部署Knative](/cn.zh-CN/Kubernetes集群用户指南/Knative/Knative组件管理/一键部署Knative.md)。
-   在Serverless Kubernetes （ASK）集群中安装部署Knative，详细介绍请参见[开启Knative](/cn.zh-CN/Serverless Kubernetes集群用户指南/Knative/Knative组件管理/开启Knative.md)。

    **说明：** 如果您的ASK集群版本<1.16，请先升级ASK集群。


## Knative计费说明

Knative本身不收取管理费用，但在使用过程中所创建的云服务器、负载均衡实例、NAT网关等，按照相应资源的价格计费。

-   [云服务器ECS计费概述](/cn.zh-CN/产品计费/计费概述.md)
-   [NAT网关计费说明](/cn.zh-CN/购买指南/NAT网关计费说明.md)
-   [负载均衡按量计费](/cn.zh-CN/传统型负载均衡CLB/CLB产品计费/按量计费.md)

## Knative交流群

如果您对于Knative有任何疑问，欢迎使用钉钉扫描二维码或者搜索群号（23302777）加入钉钉交流群。

![交流群](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5626324161/p210838.png)

## 更多信息

关于Knative更多内容请参见[Knative系列文章](https://yq.aliyun.com/articles/719274)。

**相关文档**  


[一键部署Knative](/cn.zh-CN/Kubernetes集群用户指南/Knative/Knative组件管理/一键部署Knative.md)

[t1040500.md\#](/cn.zh-CN/Kubernetes集群用户指南/Knative/Knative服务管理/快速部署Serverless应用.md)

