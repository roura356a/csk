Kubernetes对接ECI 
====================================

ECI为Kubernetes提供了基础的容器Pod运行环境，但业务间的依赖、负载均衡、弹性伸缩、定期调度等能力依然需要Kubernetes来提供。本文为您介绍Kubernetes与ECI如何对接，使用ECI作为Pod的运行资源。

对接方式概述 
---------------------------

ECI为Kubernetes提供一种层次化的解决方案：即ECI负责底层Pod容器资源的调度和管理工作，Kubernetes在ECI之上作为PaaS层来管理业务负载（例如：Deployment、Service、StatefulSet、CronJob)。

ECI在接管Pod容器底层基础设施的管理工作后，Kubernetes不再需要直接负责单个Pod的放置、启动等工作，也不再需要关心底层虚拟机的资源情况，通过ECI即可确保Pod需要的资源随时可用。

基于Kubernetes社区的Virtual Kubelet技术，ECI通过虚拟节点与Kubernetes实现无缝对接，使得Kubernetes集群可以轻松获得极大的弹性能力，而不必受限于集群的节点计算容量。更多信息，请参见[Virtual Kubelet](https://virtual-kubelet.io/docs/architecture/?spm=a2c4g.11186623.2.14.333d1d62vcFRYN)。

ECI与Kubernetes的对接分为以下两种方式： 



* 完全基于ECI

  部署Serverless Kubernetes集群，整个Kubernetes集群运行在ECI上，您不再需要关注底层虚拟机的运维和容量问题。
  

* 混合使用ECI和传统服务器

  ECI作为虚拟节点加入到Kubernetes集群中，可以提升集群的资源利用率和弹性效率，降低运行成本。
  




完全基于ECI（Serverless Kubernetes) 
---------------------------------------------------

依托ECI免运维的特性，Kubernetes可以完全依托ECI来构建，即所有Pod均运行在ECI上，Kubernetes仅需要负责管理业务负载，以保证业务的稳定运行。

如果您正在进行Kubernetes集群的选型，强烈推荐您选用阿里云容器服务Serverless Kubernetes（ASK）。ASK可以为您提供完全基于ECI运行的Kubernetes集群，为您的在线和离线业务、仿真环境、开发测试环境等提供免运维、低成本的Kubernetes环境。

关于如何通过ASK使用ECI，请参见[ASK使用ECI]()。

混合使用ECI和传统服务器 
----------------------------------

如果您已经建立了Kubernetes集群，可以把ECI作为虚拟节点加入到集群中。对于长时间运行的业务负载，您可以将此类负载的弹性流量部分调度至ECI，缩短弹性扩容的时间，减少弹性部分的扩容成本，并尽可能充分利用已有资源。当业务流量下降后，Kubernetes集群可以快速释放部署在ECI上的Pod从而降低您的使用成本。

根据您已有Kubernetes集群的情况，对接ECI包括以下几种方式：

* 如果您使用阿里云容器服务Kubernetes（ACK）部署集群，可以通过容器服务管理控制台直接添加虚拟节点来使用ECI，更多信息，请参见[ACK使用ECI]()。

  

* 如果您在阿里云ECS上自建了Kubernetes集群，需要部署Virtual Kubelet来使用ECI，更多信息，请参见[自建Kubernetes集群使用ECI]()。

  

* 如果您在线下IDC或者其他云上部署Kubernetes集群，需要部署Virtual Kubelet来使用阿里云的ECI，更多信息，请参见[线下Kubernetes集群使用ECI]()。如需进一步帮助，可咨询与您对接的阿里云解决方案架构师。

  



