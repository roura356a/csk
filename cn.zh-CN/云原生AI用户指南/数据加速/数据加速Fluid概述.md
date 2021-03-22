---
keyword: [Fluid概述, 数据加速]
---

# 数据加速Fluid概述

Fluid是一个开源的Kubernetes原生的分布式数据集编排和加速引擎，主要服务于云原生场景下的数据密集型应用，例如大数据应用、AI应用等。本文介绍数据加速的核心功能和重要概念。

## Fluid功能介绍

Fluid通过定义数据集资源，实现如下图所示的功能：

![ai-1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9873506161/p233036.png)

-   数据集抽象原生支持：将数据密集型应用所需基础支撑能力功能化，实现数据高效访问并降低多维管理成本。
-   云上数据预热与加速：Fluid通过使用分布式缓存引擎（Alluxio/JindoFS inside）为云上应用提供数据预热与加速，同时可以保障缓存数据的可观测性、可迁移性和自动化的水平扩展。
-   数据应用协同编排：在云上调度应用和数据时，同时考虑两者特性与位置，实现协同编排，提升性能。
-   多命名空间管理支持：您可以创建和管理不同Namespace的数据集。
-   异构数据源管理：一次性统一访问不同来源的底层数据（对象存储，HDFS和Ceph等存储），适用于混合云场景。

## Fluid重要概念

-   Dataset：数据集是逻辑上相关的一组数据的集合，会被运算引擎使用。例如，大数据的Spark和AI场景的TensorFlow。而这些数据智能的应用会创造工业界的核心价值。Dataset的管理实际上也有多个维度，例如安全性、版本管理和数据加速。
-   Runtime：实现数据集安全性、版本管理和数据加速等能力的执行引擎，定义了一系列生命周期的接口。可以通过实现这些接口，支持数据集的管理和加速。
-   JindoRuntime：来源于阿里云EMR团队JindoFS，是基于C++实现的支撑Dataset数据管理和缓存的执行引擎，支持OSS对象存储。JIndoFS是阿里云的产品，有专门的产品级支持。Fluid通过管理和调度JIndoRuntime实现数据集的可见性、弹性伸缩和数据迁移。相关操作文档，请参见[JindoFS加速OSS文件访问](/cn.zh-CN/云原生AI用户指南/数据加速/JindoFS加速OSS文件访问.md)和[JindoFS加速ResNet50模型训练](/cn.zh-CN/云原生AI用户指南/数据加速/JindoFS加速ResNet50模型训练.md)。
-   AlluxioRuntime：来源于[Alluixo](https://www.alluxio.org/)社区，是支撑Dataset数据管理和缓存的执行引擎实现，支持PVC，Ceph，CPFS加速，有效支持混合云场景。但是Alluxio是开源社区方案，对于数据缓存的稳定性和性能优化会和社区一起推动，但是时效性和响应会有延时。Fluid通过管理和调度AlluxioRuntime实现数据集的可见性、弹性伸缩和数据迁移。具体操作，请参见[Fluid使用文档](https://github.com/fluid-cloudnative/fluid/blob/master/docs/zh/TOC.md)。

|特性|Alluxio|Jindo|
|--|-------|-----|
|底层存储类型|PVC、Ceph、HDFS、CPFS、NFS和OSS等|OSS|
|支持方式|开源社区|阿里云产品|
|实现语言|Java|C++|
|是否支持Prometheous|是|否|

**相关文档**  


[JindoFS加速OSS文件访问](/cn.zh-CN/云原生AI用户指南/数据加速/JindoFS加速OSS文件访问.md)

[JindoFS加速ResNet50模型训练](/cn.zh-CN/云原生AI用户指南/数据加速/JindoFS加速ResNet50模型训练.md)

