---
keyword: ACK@Edge发布版本说明
---

# ACK@Edge发布Kubernetes 1.18版本说明

阿里云边缘容器服务ACK@Edge是基于容器服务ACK针对边缘计算场景推出的云边一体化托管方案。本文介绍ACK@Edge发布Kubernetes 1.18版本所做的变更内容。

## 云边运维通道和运维监控

该版本对云边运维通道和运维监控方案进行了优化，具体如下：

-   **tunnel-server**拦截并处理边缘运维监控流量的方式，从基于单机**iptables**规则改为基于集群内DNS解析。
-   依赖通道能力的监控组件**metrics-server**、**promethues**等，可以不必与**tunnel-server**部署在同一节点。
-   **tunnel-server**支持多副本部署并实现全局负载均衡。
-   云边运维通道增加`meta server`模块，用于处理`prometheus metrics`和`debug/pprof`，其中**tunnel-server**的访问端点为`http://127.0.0.1:10265`，**edge-tunnel-agent**的访问端点为`http://127.0.0.1:10266`，访问端点中的端口可以通过组件启动参数 --meta-port配置。

## 边缘节点自治

该版本对边缘缓存、健康检查、服务端点及流量统计等进行优化，同时对边缘流量自治和边缘侧应用通过InCluster模式访问**kube-apiserver**进行了增强，具体如下：

-   **edge-hub**支持边缘侧Service流量拓扑功能，该功能不再依赖Kubernetes相关FeatureGate。
-   **edge-hub**自动修改边缘侧`kubernetes service`的endpoint为**kube-apiserver**的公网endpoint，支持边缘侧应用通过InCluster模式访问Kubernetes集群。
-   **edge-hub**支持缓存CRD资源，例如：用于保存Flannel网络信息CRD（`nodenetworkconfigurations`）的缓存。
-   **edge-hub**对云端健康检查的机制进行了优化，使用`Lease`心跳替换`healthz`请求。
-   **edge-hub**监听端口拆分，由`10261`拆分为`10261`和`10267`，其中`10261`用于处理请求转发，`10267`用于处理**edge-hub**本地请求（例如：`yurthub`的`liveness probe`、`metrics`和`pprof`等\)。
-   **edge-hub**增加`metrics`指标：node\_edge\_hub\_proxy\_traffic\_collector，用于展示边缘节点上各个组件（例如：**kubelet**、**kube-proxy**）访问Kubernetes资源（例如：Pod、Deployment）时所产生的流量。

## 边缘单元化管理

单元化部署（UnitedDeployment）新增Patch功能，该功能支持对每个节点池NodePool的部署配置做定制。例如，当同一个UnitedDeployment中，不同节点池使用各不相同的本地镜像仓库时，您可以通过设置Patch字段，修改每个NodePool所使用的镜像地址。

## 边缘节点接入

边缘集群支持接入OS是Ubuntu 20.04的节点。

## 边缘网络

-   Flannel云边网络流量优化：不再**list-watch node**，改为**list-watch**自定义CRD，从而降低云边网络流量。
-   边缘流量管理Annotation调整
    -   1.16版本的相关**Annotation Key**参数说明如下：

        |Annotation Key|Annotation Value|说明|
        |--------------|----------------|--|
        |openyurt.io/topologyKeys|kubernetes.io/hostname|限制Service只能被本节点访问。|
        |openyurt.io/topologyKeys|kubernetes.io/zone|限制Service只能被本节点池的节点访问。|
        |无|无|对Service不做任何拓扑限制。|

    -   在1.18版本，对上表第二行中的参数openyout.io/topologyKeys的**Annotation Value**做了调整。该参数支持两个值：`kubernetes.io/zone`和`openyurt.io/nodepool`，这两个值都用于限制Service只能被本节点池的节点访问，且推荐您使用`openyurt.io/nodepool`。

