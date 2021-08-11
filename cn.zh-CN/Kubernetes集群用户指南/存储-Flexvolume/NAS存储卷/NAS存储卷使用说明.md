---
keyword: [NAS存储卷, 静态存储卷, 动态存储卷]
---

# NAS存储卷使用说明

您可以在容器服务Kubernetes集群中使用阿里云NAS存储卷。本文介绍NAS存储卷的使用场景、注意事项等。

## 前提条件

使用NAS数据卷之前，您需要在[文件存储管理控制台](https://nas.console.aliyun.com/)上创建文件系统，并在文件系统中添加挂载点。创建的NAS文件系统挂载点需要和您的集群位于同一VPC。

例如，您的挂载点为`055f84ad83-ixxxx.cn-hangzhou.nas.aliyuncs.com`格式。

## 背景信息

目前阿里云NAS支持两种Kubernetes挂载驱动：

-   静态存储卷挂载
    -   通过Volume方式挂载
    -   通过PV/PVC方式挂载
-   动态存储卷挂载

## 使用场景

-   静态存储卷

    NAS为共享存储，多数场景都可以通过静态存储卷挂载满足您的需求。

-   动态存储卷

    当为不同的用户（应用）提供不同的NAS子目录进行挂载时，可以考虑使用NAS动态存储卷。

    当使用StatefulSet部署应用，每个Pod配置使用不同NAS数据卷时，可以使用NAS动态存储卷。


## 推荐的挂载方式

建议您按照以下推荐的方式来使用。

-   推荐的存储驱动

    建议您通过阿里云Flexvolume驱动来使用文件存储。

    阿里云托管的Kubernetes集群会在创建集群时默认安装Flexvolume驱动，您只需要确认驱动版本是最新即可，如果驱动不是最新版本，请参见[升级组件](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/安装与升级Flexvolume组件.md)。

-   推荐的挂载方式

    出于灵活性和运维复杂度考虑，建议您使用PV/PVC的方式来挂载使用文件存储，尽量避免使用Volume方式挂载。

    -   静态存储卷挂载，请参见[NAS静态存储卷](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/NAS存储卷/NAS静态存储卷.md)。
    -   动态存储卷挂载，请参见[NAS动态存储卷](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/NAS存储卷/NAS动态存储卷.md)。
-   不推荐的挂载方式

    建议您使用PV/PVC挂载，避免使用Volume方式挂载。容器服务仅支持通过阿里云Flexvolume存储驱动挂载，不支持Kubernetes原生NFS驱动。


## 注意事项

-   NAS为共享存储，可以同时为多个Pod提供共享存储服务，即一个PVC可以同时被多个Pod使用。
-   在没有卸载NAS文件系统前，请务必不要删除NAS挂载点，否则会造成操作系统无响应。
-   创建NAS挂载点后，请等待一定时间，待挂载点**状态**为**可用**后方可使用。
-   数据卷挂载协议推荐使用NFSv3。
-   使用NAS数据卷前，推荐升级Flexvolume到最新版本。
-   极速型NAS只支持NFSv3，挂载参数需要添加`nolock`。

