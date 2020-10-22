# Kubernetes与ECI

阿里云弹性容器实例（Elastic Container Instance）为Kubernetes提供了基础的容器Pod运行环境，但业务间的依赖、负载均衡、弹性伸缩、定期调度等能力依然需要Kubernetes来提供。本文为您介绍如何通过Kubernetes集群与ECI对接，使用ECI作为Pod的运行资源。

## Kubernetes如何与ECI结合

阿里云弹性容器实例（Elastic Container Instance）为Kubernetes提供一种层次化的解决方案：即ECI负责底层Pod容器资源的调度和管理工作，Kubernetes在ECI之上作为PaaS层来管理业务负载（例如：Deployment、Service、StatefulSet、CronJob\)。

ECI在接管Pod容器底层基础设施的管理工作后，Kubernetes不再需要直接负责单个Pod的放置、启动等工作，也不再需要关心底层VM的资源情况，通过ECI确保Pod需要的资源随时可用。

ECI与Kubernetes的结合可以分为两种方式：

-   完全基于ECI。整个Kubernetes集群运行在ECI上，您不再需要关注底层VM的运维和容量问题。
-   混合使用ECI和传统服务器。提升Kubernetes集群的资源利用率和弹性效率，并降低弹性和Job流量的运行成本。

## 完全基于ECI（Serverless Kubernetes容器服务\)

依托ECI免运维的特性，Kubernetes可以完全依托 ECI 来进行构建，即所有Pod均运行在ECI上。Kubernetes仅需要负责管理业务负载，以便维持您业务的稳定运行。

[阿里云Serverless Kubernetes容器服务](https://www.aliyun.com/product/ask)，为您提供完全基于ECI运行Kubernetes集群，详情请参见[通过 Serverless Kubernetes 使用 ECI]()。

若您正在进行Kubernetes集群的选型，强烈推荐您选用阿里云Serverless Kubernetes容器服务，为您的在/离线业务、仿真环境、开发测试环境提供免运维、低成本的Kubernetes环境。

## 混合使用ECI和传统服务器

若您已经建立Kubernetes集群，通过把ECI作为虚拟节点加入，可以提升集群的利用率、提升弹性扩容的效率并减少对应的运行成本。对于长时间运行的业务负载，您可以将此类负载的弹性流量部分调度至ECI，缩短弹性扩容的时间，减少弹性部分的扩容成本，并尽可能充分利用已有资源降低成本。

当业务流量下降后，Kubernetes集群可以快速释放部署在ECI上的Pod从而降低您的使用成本。

已有Kubernetes集群的对接方式，依据您集群特征支持3种方式：

-   若您使用[阿里云Kubernetes容器服务托管版](https://www.aliyun.com/product/kubernetes)，可以在容器服务控制台直接添加虚拟节点，详情请参见[通过虚拟节点对接Kubernetes集群]()。

-   若您在阿里云ECS上自建Kubernetes集群，需要通过安装virtual kubelet来使用ECI，安装步骤请参见 [部署Virtual Kubelet with ECI]()。
-   若您在线下IDC或者其他云上部署Kubernetes集群，也可以通过安装virtual kubelet来使用阿里云的云上弹性ECI资源，详情请咨询与您对接的阿里云解决方案架构师。

