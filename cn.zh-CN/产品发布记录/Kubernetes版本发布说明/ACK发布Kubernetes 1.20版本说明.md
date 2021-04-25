---
keyword: [Kubernetes, 1.20, ACK, 变更说明]
---

# ACK发布Kubernetes 1.20版本说明

阿里云容器服务ACK严格遵循社区一致性认证。本文介绍ACK发布Kubernetes 1.20版本所做的变更说明。

## 版本升级说明

ACK针对Kubernetes 1.20版本提供了全链路的组件优化和升级。

|核心组件|版本号|升级注意事项|
|----|---|------|
|Kubernetes|1.20.4|-   当您的ACK集群升级到1.20及以上Kubernetes版本前，请确保集群内部署的Admission Webhook组件自签发的服务端证书已经包含必要的SAN字段。更多信息，请参见[Helm Chart配置示例](https://github.com/helm/helm/issues/9046#issuecomment-750892734)。
-   删除selfLink字段，不再支持。更多信息，请参见[Stop setting SelfLink in kube-apiserver](https://github.com/kubernetes/kubernetes/pull/94397)。
-   对于专有版的Master节点标签，ACK默认使用`node-role.kubernetes.io/control-plane`，同时在后续版本（不包含1.20）废弃使用`node-role.kubernetes.io/master`。 |
|Docker Runtime|19.03.5|无|
|Containerd Runtime|1.4.4|无|
|etcd|3.4.3|无|
|CoreDNS|1.7.0|-   新版本删除了对已弃用插件Upstream的兼容。如果Corefile配置项中包含Upstream插件，Upstream插件会在升级过程中被安全、自动地删除。
-   更新指标（Metrics）名称。如果您的监控系统依赖于CoreDNS指标，请注意修改。更多信息，请参见[Metric Changes](https://coredns.io/2020/06/15/coredns-1.7.0-release/#metric-changes)。 |
|NVIDA Container Runtime|3.4.1|无|

## 版本解读

**资源变更与弃用**

-   废弃Docker运行时：Docker运行时的支持在1.20被标记为废弃，目前您可以继续在集群中使用Docker。社区计划在后续版本中移除。该变动与集群所使用的容器镜像（Image）无关。您依然可以使用Docker构建您的镜像。更多信息，请参见[Dockershim Deprecation FAQ](https://kubernetes.io/blog/2020/12/02/dockershim-faq/)。
-   当您的ACK集群升级到1.20及以上Kubernetes版本前，请确保集群内部署的Admission Webhook组件自签发的服务端证书已经包含必要的SAN字段。更多信息，请参见[Helm Chart配置示例](https://github.com/helm/helm/issues/9046#issuecomment-750892734)。
-   API优先级和公平性（ APF ）是Kubernetes默认开启的测试版（Beta）特性。您可以通过此特性对不同的请求进行限制和优先级划分。更多信息，请参见[API Priority and Fairness](https://kubernetes.io/docs/concepts/cluster-administration/flow-control/)。
-   对于专有版的Master节点标签，ACK默认使用`node-role.kubernetes.io/control-plane`，同时在后续版本（不包含1.20）废弃使用`node-role.kubernetes.io/master`。
-   删除selfLink字段，不再支持。更多信息，请参见[Stop setting SelfLink in kube-apiserver](https://github.com/kubernetes/kubernetes/pull/94397)。
-   Ingress和IngressClass的资源`extensions/v1beta1` API和`networking.k8s.io/v1beta1` API已经废弃，并且会在1.22版本之后被移除。请使用`networking.k8s.io/v1`替代。

**功能增强**

-   kubelet修复探测超时时间对EXEC探测方式不准的问题。修复之后默认的EXEC探测方式的探测超时时间默认为1秒（超时时间较短）。如果您未对EXEC探测方式的探测超时时间设置特定值，建议您加上此配置。
-   API优先级和公平性（ APF ）是Kubernetes默认开启的测试版（Beta）特性。您可以通过此特性对不同的请求进行限制和优先级划分。更多信息，请参见[API Priority and Fairness](https://kubernetes.io/docs/concepts/cluster-administration/flow-control/)。
-   端点切片（Endpoint Slices）特性默认启动。从1.19开始，kube-proxy默认启用Endpoint Slices，支持更大规模的集群。更多信息，请参见[endpoint-slices](https://kubernetes.io/docs/concepts/services-networking/endpoint-slices/)。
-   不可修改ConfigMap和Secret。不可修改ConfigMap和Secret特性进入测试版（Beta）。如果设置ConfigMap或Secret为不可变，将不再允许修改，以降低kube-apiserver压力。更多信息，请参见[Immutable ConfigMaps](https://kubernetes.io/docs/concepts/configuration/configmap/#configmap-immutable)。

## ACK对Kubernetes 1.20版本的增强

**管控面增强**

-   观测性：对Request、Watch等机制增加了诸多指标（Metrics），增加了Master组件的可观测性。
-   稳定性：增加对存储层（etcd）的保护，避免了集群启动时，过多请求直接穿透到etcd，增强了系统的稳定性。
-   性能优化：通过增加索引，加速了List请求，减少kube-apiserver CPU消耗。

**Windows容器增强**

-   支持并默认开启端点切片（Endpoint Slices）。
-   支持Device Plugin。更多信息，请参见[Device Plugins](https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/device-plugins/?spm=a2c4e.11153959.blogcont498185.15.3ae63614Bg2zWO)。

