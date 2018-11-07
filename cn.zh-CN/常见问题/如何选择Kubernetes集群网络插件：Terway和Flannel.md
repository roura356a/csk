# 如何选择Kubernetes集群网络插件：Terway和Flannel {#concept_qsd_ckm_q2b .concept}

本文详细介绍了容器服务在Kubernetes集群创建时提供的两种网络插件：Terway和Flannel，为您在创建集群选择网络插件时提供参考。

容器服务通过将Kubernetes网络和阿里云VPC的深度集成，提供了稳定高性能的容器网络。在容器服务中，支持以下类型的互联互通：

-   同一个容器集群中，Pod之间相互访问。
-   同一个容器集群中，Pod访问Service。
-   同一个容器集群中，ECS访问Service。
-   Pod直接访问同一个VPC下的ECS\(\*\)。
-   同一个VPC下的ECS直接访问Pod\(\*\)。

**说明：** \* 需要正确设置安全组规则。

在创建Kubernetes集群时，阿里云容器服务提供两种网络插件：Terway和Flannel：

-   Flannel：使用的是简单稳定的社区的[Flannel](https://github.com/coreos/flannel) CNI插件，配合阿里云的VPC的高速网络，能给集群高性能和稳定的容器网络体验，但功能偏简单，支持的特性少，例如：不支持基于Kubernetes标准的Network Policy。
-   Terway：是阿里云容器服务自研的网络插件，功能上完全兼容Flannel，支持将阿里云的弹性网卡分配给容器，支持基于Kubernetes标准的NetworkPolicy来定义容器间的访问策略，支持对单个容器做带宽的限流。对于不需要使用Network Policy的用户，可以选择Flannel，其他情况建议选择Terway。
    -   使用弹性网卡：

        预先给Worker节点绑定好弹性网卡，并重启节点，然后在pod的`annotation`中配置： `k8s.aliyun.com/eni: "true"`，在容器创建时就会使用预先绑定的弹性网卡了。

    -   使用NetworkPolicy：

        参见[https://kubernetes.io/docs/concepts/services-networking/network-policies/](https://kubernetes.io/docs/concepts/services-networking/network-policies/)。

        Terway的Network Policy是通过集成Calico的Felix组件实现的，因此Network Policy的能力和Calico完全一致。对于最初为了使用Calico而自建集群的客户，目前可以通过Terway转换到容器服务Kubernetes上来。

        Terway目前集成的Felix版本为2.6.6。

    -   对Pod做带宽限制：

        在Pod的annotation中分别通过`k8s.aliyun.com/ingress-bandwidth`, `k8s.aliyun.com/egress-bandwidth` 可以指定Pod的最大入网带宽和出网带宽， 例如：`k8s.aliyun.com/ingress-bandwidth: 1m`，`k8s.aliyun.com/egress-bandwidth: 1m`。


