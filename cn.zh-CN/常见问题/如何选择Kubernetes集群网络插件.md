# 如何选择Kubernetes集群网络插件 {#concept_qsd_ckm_q2b .concept}

容器服务在Kubernetes集群创建时提供了两种网络插件选择，Terway和Flannel，那么创建集群时您需要选择哪个呢？下面看一下两种插件的功能：

-   Flannel: 这个网络插件使用的是简单稳定的社区的[Flannel](https://github.com/coreos/flannel) cni插件，配合阿里云的VPC的高速网络，能给集群高性能和稳定的容器网络体验，但功能偏简单，支持的特性少，目前建议在生产环境中选择Flannel的网络插件。
-   Terway: 这个网络插件是阿里云容器服务自研的网络插件，支持将阿里云的弹性网卡分配给容器，支持Kubernetes的NetworkPolicy来定义容器间的访问策略，支持对单个容器做带宽的限流，但目前该插件还在公测阶段，对于测试环境或者希望体验这些新特性的用户可以尝试这个网络插件。
    -   使用弹性网卡：

        预先给Worker节点绑定好弹性网卡，并重启节点，然后在pod的`annotation`中配置： `k8s.aliyun.com/eni: "true"`，在容器创建时就会使用预先绑定的弹性网卡了。

    -   使用NetworkPolicy:

        参见[https://kubernetes.io/docs/concepts/services-networking/network-policies/](https://kubernetes.io/docs/concepts/services-networking/network-policies/)。

    -   对Pod做带宽限制：

        在Pod的annotation中分别通过`k8s.aliyun.com/ingress-bandwidth`, `k8s.aliyun.com/egress-bandwidth` 可以指定Pod的最大入网带宽和出网带宽， 例如：`k8s.aliyun.com/ingress-bandwidth: 1m`，`k8s.aliyun.com/egress-bandwidth: 1m`


