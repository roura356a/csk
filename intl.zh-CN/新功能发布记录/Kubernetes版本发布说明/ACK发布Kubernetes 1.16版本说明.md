# ACK发布Kubernetes 1.16版本说明

阿里云容器服务Kubernetes版（ACK）严格遵循社区一致性认证。本文介绍ACK发布Kubernetes 1.16版本所做的变更说明。

## 版本升级说明

ACK针对Kubernetes 1.16版本提供了全链路的组件优化和升级。

|核心组件|版本号|升级注意事项|
|----|---|------|
|Kubernetes|1.16.9|Kubernetes 1.16.9版本修复漏洞CVE-2020-8555，详情请参见[修复kube-controller-manager SSRF漏洞CVE-2020-8555的公告](/intl.zh-CN/产品公告/漏洞修复公告/修复kube-controller-manager SSRF漏洞CVE-2020-8555的公告.md)。|
|1.16.6|Kubernetes 1.16版本对应内置的 CoreDNS为1.6.2版本。相较于Kubernetes 1.14 内置的CoreDNS 1.3.1版本，新版本的CoreDNS有以下变化： -   废弃proxy插件，改用性能更高的forward插件。
-   默认开启ready插件，作为readiness的检查插件。

为了使您的Corefile兼容新版本的CoreDNS，我们会帮您自动进行Corefile的迁移工作，使您的Corefile匹配更新版本的CoreDNS。 |
|Docker|19.03.5 \(containerd 1.2.10\)|无|
|etcd|3.4.3|无|

## 版本解读

-   **性能优化**

    Kubernetes 1.16.6版本相比1.14的性能优化如下：

    -   对PodAffinity进行优化，性能提升了约100%。
    -   对序列化操作进行了优化。Pod list操作提升了约40%性能，Node list操作提升了约30%性能。
    -   提升了Server端在处理带有巨大map对象的apply请求时候的性能。
    -   优化了nodelease的心跳包方案。在一个8000节点的ACK集群中，能够每分钟降低50k次向API Servrer/etcd获取Lease的请求。
    -   Kubernetes 1.16.6版本在Pod创建速度方面有明显提升。在无状态Pod场景（Pod不用挂载configmap、secret等volume）的创建速度上：
        -   1.16.6和1.14的Pod创建时间都满足ACK sig-scalability 定义的SLA\(（99%已拉取好镜像的Pod 启动时间在5秒内）。
        -   在最差情况下（99分位点），Kubernetes 1.14版的Pod创建速度接近5秒，表现差于同指标下Kubernetes 1.16.6版的3秒。
    Docker 19.03.5版本相对之前的版本的优化如下：

    -   内置buildkit，优化镜像构建速度。
    -   runC命令优化systemd检测逻辑。容器启动速度加快，占用更小内存。
    Docker 19.03.5版本在运行时稳定性上提升如下：

    -   修复Pod使用exec健康检查时Pod偶发重启的问题。
    -   修复运行命令docker cp时的安全性风险漏洞CVE-2018-15664。
    -   修复容器包含多进程（富容器）退出时Docker不响应的问题。
    -   修复containerd的句柄泄露问题。
-   **功能增强**

    Kubernetes 1.16.6版本相较于1.14版本的演进和增强值得注意的变更主要为以下几个方面。

    -   extensions/v1beta1、apps/v1beta1、apps/v1beta2的API均不再被默认支持。其中，所有apps/v1beta1和apps/v1beta1下的资源使用apps/v1替代；位于extensions/v1beta1下的资源daemonsets、deployments、replicasets将使用apps/v1替代；位于extensions/v1beta1下的资源networkpolicies使用networking.k8s.io/v1替代。

        **说明：** 为了更好地兼容您的业务，ACK会在Kubernetes 1.16.6版本开启对上述API的兼容，并在1.18版本再彻底废除对其支持。建议您尽快调整这些API。

    -   已废弃的kubelet安全控制参数AllowPrivileged、HostNetworkSources、HostPIDSources和HostIPCSources 已被移除，取而代之的是一些准入控制（例如PodSecurityPolicy）来增强这些限制。
    -   多个功能进入更加稳定的阶段，例如CRD和Admission webhook在1.16.6版本中进入到了GA阶段。

## ACK对Kubernetes 1.16.6版本的增强

ACK针对Kubernetes 1.16版本做了以下方面的增强：

-   稳定性和性能增强
    -   为幂等函数添加重试，提高集群创建成功率。
    -   在升级kubelet的过程中不重启存量容器。
    -   解决hugetlb导致kubelet启动失败问题。
-   可观测性增强
    -   优化负载均衡SLB到apiserver的探活日志。
    -   调整aggregationcontroller日志等级。
    -   优化阿里云容器服务托管版集群中命令get cs的输出结果。
    -   在summary和container指标接口上增强安全沙箱容器的指标。

