---
keyword: [image-syncer, 迁移容器镜像]
---

# 通过image-syncer工具迁移容器镜像

在Kubernetes集群迁移场景中，镜像仓库之间进行镜像迁移和同步是基本需求，image-syncer工具可以解决通用的容器镜像批量迁移和镜像同步复制的问题，将已有的容器镜像平滑的迁移到阿里云镜像服务ACR上。本文主要为您介绍如何通过image-syncer工具迁移容器镜像。

阿里云上的容器服务ACK在使用成本、运维成本、方便性、长期稳定性上大大超过云厂商自建自维护Kubernetes集群，有不少云厂商纷纷想把之前自己维护Kubernetes负载迁移到阿里云ACK服务上。在迁移过程中，当镜像个数较少时，可以通过docker pull或docker push命令完成镜像迁移，如果涉及到成千上百个镜像，甚至几T的镜像仓库数据时，迁移过程就变得非常漫长，并且可能丢失数据。此时，用户在各种容器镜像仓库之间迁移时，期望有镜像同步复制的能力。阿里云开源image-syncer工具具备此能力并已经帮助多家云厂商成功迁移镜像，其中最大镜像仓库的总量达到3TB以上。同步任务时能跑满机器带宽，并且对进行同步任务的机器磁盘容量没有要求。

## image-syncer简介

在Kubernetes集群迁移场景中，镜像仓库之间进行镜像迁移和镜像同步复制是基本需求，而使用docker pull或docker push结合脚本的传统方式进行镜像同步，有如下几个局限性：

-   依赖磁盘存储，需要及时进行本地镜像的清理，并且落盘造成多余的时间开销，难以胜任生产场景中大量镜像的迁移。
-   依赖Docker程序，Docker Daemon对Pull和Push的并发数进行了严格的限制，无法进行高并发同步。
-   一些功能只能通过HTTP API进行操作，仅使用Docker CLI无法做到，使脚本变得复杂。

image-syncer的定位是一个简单、易用的批量镜像迁移和镜像同步复制工具，支持几乎所有目前主流的基于Docker Registry V2搭建的镜像存储服务，例如ACR、Docker、Hub、Quay、自建Harbor等，目前已经初步经过了TB级别的生产环境镜像迁移验证，详情请参见[image-syncer](https://github.com/AliyunContainerService/image-syncer?spm=a2c6h.12873639.0.0.66b165a8HrkbnA)。

## 工具特点

image-syncer提供了以下几项支持：

-   支持多对多镜像仓库同步。
-   支持基于Docker Registry V2搭建的Docker镜像仓库服务

    例如，Docker Hub、Quay、 阿里云镜像服务ACR、Harbor等。

-   镜像同步复制只经过内存和网络，不依赖磁盘存储，同步速度快。
-   支持增量同步。

    通过对同步过的镜像blob信息落盘，不会对已同步的镜像进行重复同步。

-   支持并发同步。

    可以通过配置文件调整并发数。

-   支持自动重试失败的同步任务，解决大部分镜像同步中的网络抖动问题。
-   不依赖Docker以及其他程序。

通过使用image-syncer，只需要保证image-syncer的运行环境与需要同步的registry网络连通，您可以快速地完成从镜像仓库的迁移、复制以及增量同步，并且image-syncer对硬件资源几乎没有要求（因为image-syncer严格控制网络连接数目=并发数，所以只有在单个镜像层过大的情况下，并发数目过大可能会打满内存，内存占用 <= 并发数x最大镜像层大小）。除了使用重传机制规避同步过程中可能出现的偶发问题之外，image-syncer会在运行结束时统计最后同步失败的镜像个数，并且打印出详细的日志，帮助使用者定位同步过程中出现的问题。

## 准备工作

使用image-syncer时，您只需要提供一个配置文件，内容示例如下：

```
{
    "auth": {                   // 认证字段，其中每个对象为一个registry的一个账号和
                                // 密码；通常，同步源需要具有pull以及访问tags权限，
                                // 同步目标需要拥有push以及创建仓库权限，如果没有提供，则默认匿名访问

        "quay.io": {            // registry的url，需要和下面images中对应registry的url相同
            "username": "xxx",               // 用户名，可选
            "password": "xxxxxxxxx",         // 密码，可选
            "insecure": true                 // registry是否是http服务，如果是，insecure字段需要为true，默认是false，可选，支持这个选项需要image-syncer版本 > v1.0.1
        },
        "registry.cn-beijing.aliyuncs.com": {
            "username": "xxx",
            "password": "xxxxxxxxx"
        },
        "registry.hub.docker.com": {
            "username": "xxx",
            "password": "xxxxxxxxxx"
        }
    },
    "images": {
        // 同步镜像规则字段，其中一条规则包括一个源仓库（键）和一个目标仓库（值）

        // 同步的最大单位是仓库（repo），不支持通过一条规则同步整个namespace以及registry

        // 源仓库和目标仓库的格式与docker pull/push命令使用的镜像url类似（registry/namespace/repository:tag）
        // 源仓库和目标仓库（如果目标仓库不为空字符串）都至少包含registry/namespace/repository
        // 源仓库字段不能为空，如果需要将一个源仓库同步到多个目标仓库需要配置多条规则
        // 目标仓库名可以和源仓库名不同（tag也可以不同），此时同步功能类似于：docker pull + docker tag + docker push
        "quay.io/coreos/kube-rbac-proxy": "quay.io/ruohe/kube-rbac-proxy",
        "xxxx":"xxxxx",
        "xxx/xxx/xx:tag1,tag2,tag3":"xxx/xxx/xx"
        // 当源仓库字段中不包含tag时，表示将该仓库所有tag同步到目标仓库，此时目标仓库不能包含tag
        // 当源仓库字段中包含tag时，表示只同步源仓库中的一个tag到目标仓库，如果目标仓库中不包含tag，则默认使用源tag
        // 源仓库字段中的tag可以同时包含多个（比如"a/b/c:1,2,3"），tag之间通过","隔开，此时目标仓库不能包含tag，并且默认使用原来的tag

        // 当目标仓库为空字符串时，会将源镜像同步到默认registry的默认namespace下，并且repo以及tag与源仓库相同，默认registry和默认namespace可以通过命令行参数以及环境变量配置，参考下面的描述
    }     
}
```

## 使用示例

-   [从自建Harbor同步镜像到ACR默认实例版](/intl.zh-CN/最佳实践/自建Kubernetes迁移ACK/容器镜像迁移/从自建Harbor同步镜像到ACR默认实例版.md)
-   [从自建Harbor同步镜像到ACR企业版](/intl.zh-CN/最佳实践/自建Kubernetes迁移ACK/容器镜像迁移/从自建Harbor同步镜像到ACR企业版.md)

