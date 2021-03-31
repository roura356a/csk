---
keyword: [自建Kubernetes迁移, 迁移方案]
---

# Kubernetes迁移方案概述

本文整体简单介绍下如何通过6个步骤，将自建Kubernetes集群平滑迁移到阿里云Kubernetes集群，并尽量确保迁移期间对业务无影响。

## 迁移方案

![迁移方案](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0763659951/p73923.png)

## 迁移步骤

1.  阿里云Kubernetes集群资源配置。

    由运维人员完成阿里云Kubernetes集群创建及集群维度资源配置，降低开发人员应用迁移复杂度。详情请参见[创建Kubernetes专有版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes专有版集群.md)。

    集群创建和配置过程中，以下配置项为必选。

    -   集群模版：您可以根据业务需要选择以下集群模板。
        -   标准专有集群。

            Kubernetes集群的Master和Worker由用户创建并管理，对集群享有完整的控制能力。您需要承担Master和Worker的资源费用。

        -   标准托管集群。

            Kubernetes集群的Master由容器服务托管，您只需创建并管理Worker，免Master运维。您需要承担Worker的资源费用。

    -   **操作系统**：您可以根据需要选择操作系统。
        -   推荐您使用阿里云Kubernetes集群默认的操作系统，CentOS7.6或AliyunLinux 2.1903。
        -   若您对系统内核版本有要求，可使用自定义镜像，详情请参见[使用自定义镜像创建ACK集群](/cn.zh-CN/最佳实践/自建Kubernetes迁移ACK/使用自定义镜像创建ACK集群.md)。
    -   **专有网络**：您需要设置VPC和交换机信息。
    -   **配置 SNAT**：您需要为专有网络配置SNAT。
    -   **公网访问**：您需要使用EIP暴露API Server。
    -   **云监控插件**：您需要在ECS节点上安装云监控插件。
    -   **日志服务**：您需要安装日志服务插件。
2.  数据迁移。
    -   数据库迁移。
        1.  创建RDS数据库。
        2.  配置数据库白名单。
        3.  配置PrivateZone。

            通过PrivateZone可以将数据库域名解析到RDS域名，减少应用改造。

        4.  迁移MySQL数据。

            通过DTS迁移MySQL数据，支持全量、增量、双向同步。详情请参见[从自建MySQL迁移至RDS MySQL](/cn.zh-CN/数据迁移/从自建数据库迁移至阿里云/源库为MySQL/从自建MySQL迁移至RDS MySQL.md)。

    -   存储迁移。
        1.  开通OSS服务。
        2.  创建OSS存储空间（Bucket）。
        3.  迁移存储数据。

            使用ossimport工具把存储在服务器本地、第三方云存储（S3、Azure、腾讯COS等）中的数据批量迁移到OSS。详情请参见[说明及配置](/cn.zh-CN/常用工具/数据迁移工具ossimport/说明及配置.md)。

    -   镜像迁移。
        1.  创建ACR容器镜像仓库。
        2.  设置镜像服务访问凭证。
        3.  迁移镜像数据。

            使用image-syncer快速将容器镜像批量迁移至ACR容器镜像仓库服务。详情请参见[通过image-syncer工具迁移容器镜像](/cn.zh-CN/最佳实践/自建Kubernetes迁移ACK/容器镜像迁移/通过image-syncer工具迁移容器镜像.md)。

3.  应用配置迁移。

    由运维或者研发人员基于velero工具完成集群或者应用配置迁移。详情请参见[Kubernetes应用迁移](/cn.zh-CN/最佳实践/自建Kubernetes迁移ACK/Kubernetes应用迁移.md)。

    1.  准备迁移环境。
        1.  安装Velero客户端。
        2.  创建OSS Bucket。
        3.  创建RAM账号并生成AccessKey。
        4.  部署Velero服务端。
    2.  在自建Kubernetes集群备份应用。
        -   无PV数据的应用备份。
        -   有PV数据的应用备份。
    3.  在阿里云Kubernetes集群恢复应用。
        1.  创建StorageClass。
        2.  恢复应用。
    4.  更新应用配置。
        -   更新镜像地址。
        -   优化服务暴露方式。
        -   适配存储盘挂载方式。
    5.  调试并启动应用。
4.  应用回归测试。

    在不影响线上流量情况下，由测试人员完成阿里云Kubernetes集群业务功能回归测试。

    1.  配置应用测试域名。
    2.  测试业务功能。
    3.  确认应用日志采集。
    4.  确认应用监控。
5.  客户端流量切换。

    由运维人员做DNS切换，将流量切换至阿里云Kubernetes集群。

    1.  DNS流量切换：调整DNS解析配置实现流量切换。
    2.  客户端流量切换：升级客户端代码或配置实现流量切换。
6.  下线自建Kubernetes集群。

    由运维人员确认阿里云Kubernetes集群服务访问正常后，下线自建Kubernetes集群资源。

    1.  确认阿里云Kubernetes集群流量正常。
    2.  下线自建Kubernetes集群资源。
    3.  清理OSS Bucket中的备份文件。

