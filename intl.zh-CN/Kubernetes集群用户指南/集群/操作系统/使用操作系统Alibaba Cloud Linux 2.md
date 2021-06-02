---
keyword: [Alibaba Cloud Linux 2, ACK, 优势]
---

# 使用操作系统Alibaba Cloud Linux 2

ACK目前已经全面支持Alibaba Cloud Linux 2的节点创建，并结合Alibaba Cloud Linux 2的高内核特性提供了多场景的优化。本文介绍ACK中使用Alibaba Cloud Linux 2操作系统的优势，及ACK基于Alibaba Cloud Linux 2提供的多场景优化。

Alibaba Cloud Linux 2是新一代阿里云原生Linux操作系统，为云上应用程序提供安全、稳定、高性能的定制化运行环境，并针对云基础设施进行了深度优化，为您打造更好的运行时体验。您可以免费使用Alibaba Cloud Linux 2公共镜像，并免费获得阿里云针对该操作系统的长期支持。

## 使用Alibaba Cloud Linux 2操作系统镜像的优势

Alibaba Cloud Linux 2的操作系统是针对阿里云飞天虚拟化平台开发的，针对阿里云的环境提供了很多优化和新特性，主要包括：

-   是阿里云上启动最快的Linux发行版。
-   针对ECS大规格虚机、裸金属服务器深度优化，特别是大规格实例的多任务的场景。
-   默认安装阿里云常用软件包，如阿里云CLI、cloud-init等，减少云资源的管理成本。
-   精简系统，最小的安全攻击面，最少的系统资源占用。
-   更完善的支持体系，在阿里云上提供多种渠道的技术支持体系。
-   更及时的软件安全漏洞（CVE）修复。
-   支持内核热补丁升级的能力，大大保障漏洞修复时的业务连续性。

应用实例在Alibaba Cloud Linux 2上运行的性能优势包括：

-   针对ECS实例环境大幅优化启动速度，在系统压力来临时快速的扩容启动计算资源，启动速度相对CentOS 7提升29%。
-   针对ECS实例环境优化了多任务的场景，提升大规格实例多任务的性能，同样规格下能有16%的性能提升。
-   更高效的系统调用，系统调用的性能提升11%。
-   Linux网络栈的优化，综合网络性能相对CentOS 7提升7.8%。
-   Alibaba Cloud Linux 2默认是编译好BBR的拥塞控制算法的，在公网访问较多的场景中允许您修改拥塞控制算法为BBR（Bottleneck Bandwidth and RTT），提升公网访问的带宽稳定性。
-   针对TLS协议，进行了加密优化。
-   支持新的Budget Fair Queueing的IO调度器，降低云盘的延迟。

## ACK结合Alibaba Cloud Linux 2的场景优化

阿里云容器化业务通过内核级的优化，让容器任务混布的密度提升的同时不影响在线业务的响应。Alibaba Cloud Linux 2的操作系统和Kernel中包含了这些优化。ACK针对这些优化特性，提供了多场景的优化，促进容器化业务跑的更快更平稳。

-   **IPVS优化**
    -   场景一：大规格机器（CPU数大于64）且有大量IPVS虚拟IP的场景。

        解决问题：IPVS的统计定时器（estimation timer）会定期计算各个连接当前的速率，在连接数多的情况下，该操作会长时间占用CPU，导致网络收包不及时，引起Ping命令高达200 ms的波动。

        优化方案：将IPVS的统计定时器放到kworker中执行；同时添加sysctl命令关掉IPVS的统计。

        效果：不再有由于统计定时器引起的波动。

    -   场景二：容器滚动升级的场景。

        解决问题：在容器发生了滚动升级的时候，如果五元组没有发生变化，新的TCP SYN包命中了旧的IPVS五元组连接记录，并且需要被调度到新的目的地址的时候，IPVS默认会丢掉SYN包，导致SYN包重传，从而引发1秒的延时问题。

        优化方案：在新conntrack项已经存在的情况下，通过释放conntrack中的TIME\_WAIT状态的连接，将其调度并替换为新连接。

        效果：可以几乎没有时延的切换到新的真实节点（real server）。

-   **CoreDNS优化**

    场景一：容器内大量DNS查询导致conntrack表满的场景。

    解决问题：容器内的应用查询固定的地址（addr）或端口（port）的DNS会让相应conntrack entry变成stream mode状态。由于DNS请求的类型是UDP无状态、问答式、时间短，因此导致conntrack entry维护了很多无用的UDP conntrack entries，不能及时清理，进而可能导致conntrack表膨胀引起NAT的性能下降。

    优化方案：

    -   只有UDP连接持续2秒以上才会被设置成stream mode状态，从而避免了conntrack entry的快速膨胀。
    -   缩短默认UDP conntrack过期时间，从180s缩短到120s，让其更快过期，减少对conntrack entry的影响。
    效果：相同测试场景下UDP的conntrack表项下降到原来的一半。

-   **容器网络性能优化**

    在Alibaba Cloud Linux 2的节点上，容器服务Terway网络插件支持IPVlan的容器网络模式，在小包的场景下相对传统bridge和策略路由网络性能提升40%。Alibaba Cloud Linux 2默认编译了BBR的拥塞控制算法，在公网访问较多的场景中允许您修改容器的拥塞控制算法为BBR，提升公网访问的带宽稳定性，在容器的公网连接和跨公网的镜像拉取上性能大幅提升。

-   **安全容器支持和优化**

    阿里云与Kata Containers和Clear Linux社区合作。在弹性裸金属实例上，您可以无缝地部署整套Kata Containers的解决方案。ACK还优化了安全沙箱（RunV）镜像的启动时间，使得Kata Containers的整体解决方案可以正常运行。ACK在此之上，提供了和普通集群体验几乎一致的安全沙箱容器集群，让应用运行在一个轻量虚拟机沙箱环境中，适合于多用户间的负载隔离，对不可信应用的隔离，在提升安全性的同时，对性能影响也做到非常小。

-   **AutoScaler优化**

    Alibaba Cloud Linux 2针对ECS实例环境优化启动速度，节点的启动时间相对Centos 7的系统降低60%，结合容器服务ACK灵活高效的自动弹性伸缩，在应用压力来临时，容器服务ACK集群会自动根据负载状况创建和启动ECS的节点加入集群，并调度和启动应用实例，Alibaba Cloud Linux 2的快速的扩容启动的能力让计算资源可以急事满足流量峰值的需求。

-   **资源监控和控制能力优化**

    Alibaba Cloud Linux 2的内核提供了针对容器场景的PSI压力模型、per-cgroup kswapd、memory priority等的细粒度可视化和控制能力。在Alibaba Cloud Linux 2的ACK集群中我们可以通过CGroup Controller来利用这些能力，可以实现细粒度BufferIO Control、TCP、CPUSet、Mem、NUMA等细粒度资源的配置和动态更新，在逐步提升资源利用率的同时也能保障应用间的互相干扰降到最低。

-   **AI和数据加速优化**

    Alibaba Cloud Linux 2对大规格机型和多任务的优化可以提升高性能计算任务的速度，对存储的流式读写的优化也可以提高模型大文件的读写性能，综合起来大大加速AI和高性能计算任务的效率。实际测试场景如下：

    -   通过Alluxio利用64个线程加载OSS数据1152个文件144 GB，在CentOS需要3分25秒，Alibaba Cloud Linux 2只需2分19.037秒，速度是CentOS上的1.6倍。
    -   运行ResNet50 Batch 128模型训练，数据缓存到Alluxio中，在CentOS下V100仅仅为5212.00 images/s。在Alibaba Cloud Linux 2下V100可以达到8746.59 images/s，速度是CentOS上的1.7倍。
-   **容器资源增强展示优化**

    由于当前多容器共宿主机的形态，导致容器中直观看到的资源都是宿主机资源，对很多应用非常不友好。Alibaba Cloud Linux 2对内核的cgroup资源展示进行了优化，可以在容器内部正确显示容器所占用的资源，例如top命令、cpuinfo和meminfo接口的显示信息，大大方便了您的观测需求。

-   **其他优化**
    -   使用Linux 4.19的内核，ACK将阿里巴巴内核和容器化实践融入其中。
    -   降低Overlayfs的性能损耗，减少容器化对存储性能的损失。
    -   大量sysctl namespace化，在4.19的内核中大部分sysctl配置都支持在容器中单独设置，比如TCP的超时和重传时间，不同应用有不同的超时和重传的需求，但是在CentOS 7的内核中是修改不了的，在Alibaba Cloud Linux 2中支持对其做Pod级别的配置。

## 使用Alibaba Cloud Linux 2作为集群节点系统镜像

您可以在创建集群的配置过程中，将**操作系统**选择为**Alibaba Cloud Linux 2.1903**来使用Alibaba Cloud Linux 2作为集群节点系统镜像。具体步骤，请参见[创建Kubernetes专有版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes专有版集群.md)。

![linux](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6647452061/p96758.png)

**说明：**

如果您选用Alibaba Cloud Linux 2，在创建集群以及后期扩容节点、添加节点、自动伸缩节点时，ACK会自动检测Alibaba Cloud Linux 2的安全补丁更新并自动安装补丁。

**相关文档**  


[Alibaba Cloud Linux 2产品详情页](https://www.alibabacloud.com/zh/products/alinux)

[阿里云内核开源官网](https://alibaba.github.io/cloud-kernel/)

