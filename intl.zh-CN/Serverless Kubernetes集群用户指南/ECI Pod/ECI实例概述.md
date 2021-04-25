---
keyword: [弹性容器实例ECI, ECI限制, ECI Pod实例费用]
---

# ECI实例概述

本文主要介绍弹性容器实例ECI Pod的配置组成，包括安全隔离、CPU/Memory资源和规格配置、镜像拉取、存储、网络、日志收集等，及ECI的使用限制。

-   您需要创建在阿里云容器服务Kubernetes版（ACK）集群中部署虚拟节点，或者创建ASK（Serverless Kubernetes）集群。详情参见[步骤一：在ACK集群中部署ack-virtual-node组件](/intl.zh-CN/Kubernetes集群用户指南/弹性容器实例ECI/通过部署ACK虚拟节点组件创建ECI Pod.md)和[创建Serverless Kubernetes集群](/intl.zh-CN/Serverless Kubernetes集群用户指南/快速入门/创建Serverless Kubernetes集群.md)。
-   您需要开通弹性容器实例服务。登录[弹性容器实例控制台](https://eci.console.aliyun.com/)开通相应的服务。

## 安全隔离

弹性容器实例ECI作为安全可靠的Serverless容器运行环境，每个ECI实例底层通过轻量级安全沙箱技术完全强隔离，实例间互不影响。同时实例在调度时尽可能分布在不同的物理机上，进一步保障了高可用性。

## CPU/Memory资源和规格配置

ECI支持多种规格配置的方式来申请资源和计费。

-   **指定CPU和Memory**

    -   **指定容器的CPU和Memory**：用户可以通过Kubernetes标准方式配置单个容器的CPU和Memory（resources.limit），如不指定则默认单个容器的资源是1C2G。ECI的资源则是Pod内所有容器所需资源的总和。（注意我们会对不支持的规格进行自动规整，例如所有容器相加的资源为2C3G，那么将会自动规整为2C4G。超过4C将不会自动规整。）
    -   **指定Pod的CPU和Memory**：用户可以通过Annotation的方式配置整个Pod的CPU和Memory，这种情况将不再把所有容器资源相加，而以指定的资源创建和计费。此时可以不用指定内部容器的request和limit ，各容器可以最大程度的使用Pod内计算资源。
    目前ECI Pod支持的CPU和Memory规格有：

    |vCPU|Memory|
    |----|------|
    |.25 vCPU|0.5 GB, 1 GB|
    |.5 vCPU|1 GB, 2 GB|
    |1 vCPU|2 GB, 4 GB, 8 GB|
    |2 vCPU|2 GB, 4 GB, 8 GB, 16 GB|
    |4 vCPU|4 GB, 8 GB, 16 GB, 32 GB|
    |8 vCPU|8 GB, 16 GB, 32 GB, 64 GB|
    |12 vCPU|12 GB, 24 GB, 48 GB, 96 GB|
    |16 vCPU|16 GB, 32 GB, 64 GB, 128 GB|
    |24 vCPU|48 GB, 96 GB, 192 GB|
    |32 vCPU|64 GB, 128 GB, 256 GB|
    |52 vCPU|96 GB, 192 GB, 384 GB|
    |64 vCPU|128 GB, 256 GB, 512 GB|

    CPU和Memory配置其计费时长从挂载外部存储/下载容器镜像开始至ECI实例停止运行（进入Succeeded/Failed状态）结束。

    **计费公式**：ECI Pod实例费用 =（ECI实例CPU核数 x CPU单价 + ECI实例内存大小 x 内存单价）x ECI实例运行时长，实例按秒计费。当前计费单价如下：

    |计费项|价格|小时价|
    |---|--|---|
    |CPU（vCPU \* 秒）|0.000049 元|0.1764 元/小时|
    |内存（GB \* 秒）|0.00000613 元|0.0221 元/小时|

-   **指定Pod的ECS规格**

    ECI支持根据[ECS实例规格族](/intl.zh-CN/实例/实例规格族.md)，价格参考[各地域ECS按量](https://www.alibabacloud.com/zh/pricing)价格。您可以根据需要，指定ECI实例底层使用的ECS规格族，获得各规格族的指定能力，例如指定使用[ecs.sn1ne](/intl.zh-CN/实例/实例规格族.md)规格族来使用网络增强能力。

    **计费公式**：ECI实例费用 = ECI实例指定的[ECS规格单价](https://www.alibabacloud.com/zh/pricing) x ECI实例运行时长，实例按秒计费。

    当指定Pod的ECS规格时，ECI可以选择使用预留实例券对ECI按规格创建实例进行抵扣。详情请参见[预留实例券概述](/intl.zh-CN/实例/选择实例购买方式/预留实例券/预留实例券概述.md)。

    ECI使用预留实例券后的费用，跟您使用ECS包月实例的费用相近。

-   **抢占式实例**

    您可以通过配置Annotation使用spot可抢占式实例，大幅降低计算成本。


## 镜像拉取

ECI Pod默认每次启动后使用内部的containerd从远端拉取容器镜像。如果镜像为公共镜像，则需要开通VPC的NAT网关，或者给ECI Pod挂载EIP。建议您将容器镜像存储在阿里云镜像仓库（ACR），通过VPC网络减小镜像拉取时间。另外对于ACR上的私有镜像，实现了免密拉取功能方便您使用。

同时支持了镜像快照功能。通过把容器镜像缓存到快照中，然后通过快照快速启动容器，避免再次从远端拉取镜像，适合大镜像的场景。

## 存储

支持多种使用存储的方式：

-   Flexvolume：
    -   挂载NAS Volume。同标准的Flexvolume用法相同，指定nas volume Id进行挂载。
    -   挂载Disk Volume：同标准的Flexvolume用法相同，指定disk volume Id进行挂载。
    -   随ECI实例自动创建disk volume：为了提供更灵活的云盘挂载能力，ECI支持创建时挂载flexvolume动态创建一个云盘，可以指定disk volume的size大小，也可以配置当ECI实例结束时是否保留disk volume。
-   NFS：[参考示例](https://github.com/AliyunContainerService/serverless-k8s-examples/blob/master/nas-volume/nas-volume.yaml)。
-   PV/PVC：[参考示例](https://github.com/AliyunContainerService/serverless-k8s-examples/tree/master/pvc)

## 网络

ECI Pod默认使用Host网络模式，占用交换机VSwitch的一个弹性网卡ENI资源。

在Kubernetes集群环境中，ECI Pod与ECS节点上的Pod互联互通，方法如下：

-   LoadBalancer Service挂载ECI Pod：也可以支持service同时挂载ECS节点上的Pod和ECI Pod。
-   访问ClusterIP Service：ECI Pod可以访问集群中的clusterIP地址。
-   挂载EIP：支持给ECI Pod挂载EIP，可自动创建或者绑定到已有的EIP实例。

## 日志收集

用户可以直接配置Pod的Env收集stdout或者文件日志到阿里云日志服务SLS中。一般情况无需再部署一个logtail sidecar容器。

## 支持Annotation列表

**说明：** 注意：Annotation需要配置在Pod Spec中，而不是Deployment Spec中。

|Annotation|解释|示例|
|----------|--|--|
|k8s.aliyun.com/eci-use-specs|表示允许的实例规格，可以配置多个。当前规格没有库存时依次尝试下一个规格创建。支持CPU-MEM格式（$\{cpu\}-$\{mem\}Gi）、ECS规格格式、GPU规格格式（eci-gpu-$gputype-$gpucount）。|"k8s.aliyun.com/eci-use-specs": "2-4Gi,4-8Gi,ecs.c6.xlarge,ecigpu-P100-4"|
|k8s.aliyun.com/eci-vswitch|设置Pod的虚拟交换机。|"k8s.aliyun.com/eci-vswitch" : "$\{your\_vsw\_id\}"|
|k8s.aliyun.com/eci-security-group|设置Pod的安全组。|"k8s.aliyun.com/eci-security-group" : "$\{your\_security\_group\_id\}"|
|k8s.aliyun.com/eci-resource-group-id|设置Pod所在的资源组|"k8s.aliyun.com/eci-resource-group-id" : "$\{your\_resource\_group\_id\}"|
|k8s.aliyun.com/eci-ram-role-name|设置Pod的RamRole，赋予在ECI实例内部可以访问阿里云产品能力。|"k8s.aliyun.com/eci-ram-role-name" : "$\{your\_ram\_role\_name\}"|
|k8s.aliyun.com/eci-image-snapshot-id|指定已有ImageCacheID，加速ECI Pod创建。|k8s.aliyun.com/eci-image-snapshot-id: "$\{your\_image\_cache\_id\}"|
|k8s.aliyun.com/eci-image-cache|根据用户已有的镜像缓存，自动匹配镜像缓存。默认为false。|k8s.aliyun.com/eci-image-cache: "true"|
|k8s.aliyun.com/eci-with-eip|创建弹性公网IP，绑定到ECI Pod。|"k8s.aliyun.com/eci-with-eip": "true"|
|k8s.aliyun.com/eip-bandwidth|设置弹性公网IP带宽，如果不指定默认为5M。|"k8s.aliyun.com/eci-with-eip": "true""k8s.aliyun.com/eip-bandwidth": 10|
|k8s.aliyun.com/eci-eip-instanceid|给Pod绑定已有的弹性公网IP。|"k8s.aliyun.com/eci-eip-instanceid": "$\{your\_eip\_Instance\_Id\}"|
|k8s.aliyun.com/eci-spot-strategy|SpotAsPriceGo：系统自动出价，跟随当前市场实际价格。

SpotWithPriceLimit：设置抢占实例价格上限。

|k8s.aliyun.com/eci-spot-strategy: "SpotAsPriceGo"|
|k8s.aliyun.com/eci-spot-price-limit|只有k8s.aliyun.com/eci-spot-strategy设置为SpotWithPriceLimit时有效。设置实例每小时最高价格，支持最多3位小数。|k8s.aliyun.com/eci-spot-price-limit: "0.250"|
|k8s.aliyun.com/eci-ntp-server|设置ntp server，支持设置多个。|k8s.aliyun.com/eci-ntp-server: 100.100.5.1,100.100.5.2 \# 设置您的NTP服务器地址。|
|k8s.aliyun.com/eci-set-diskvolume|把volume（emptyDir或者hostPath）转换为动态创建云盘。格式为”$volumeName:$type:$size“。|k8s.aliyun.com/eci-set-diskvolume: "cache-volume:ext4:500Gi"|

## ECI限制

ECI和虚拟节点目前已经兼容了大部分Pod功能，以下功能暂不支持。

-   不支持在虚拟节点上运行DaemonSet Pod。
-   不支持hostPath/hostPid。
-   不支持privileged权限开放。
-   不支持NodePort类型的Service。
-   不支持Network Policy。

