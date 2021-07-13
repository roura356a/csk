---
keyword: [CoreDNS, 容器服务Kubernetes, DNS]
---

# 优化集群DNS性能

CoreDNS是Kubernetes集群中负责DNS解析的组件，能够支持解析集群内部自定义服务域名和集群外部域名。CoreDNS具备丰富的插件集，在集群层面支持自建DNS、自定义hosts、CNAME、rewrite等需求。ACK集群中默认部署形态在DNS QPS较高场景下，可能会出现解析压力，导致部分查询失败。本文介绍如何优化集群DNS性能。

-   [创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)
-   [通过kubectl管理Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl管理Kubernetes集群.md)

有关CoreDNS的更多信息，请参见[CoreDNS](https://coredns.io/)。

## 合理调整集群CoreDNS副本数

调整CoreDNS副本数与集群节点数到合适比率有助于提升集群服务发现的性能，该比值推荐为1:8，即一个CoreDNS Pod支撑8个集群节点。

-   当集群节点无需大规模扩缩容时，执行以下命令调整目标副本数到目标值。

    ```
    kubectl scale --replicas={target} deployment/coredns -n kube-system
    ```

    **说明：** 将目标副本数`{target}`设置成目标值。

-   集群节点需要大规模扩缩容时，推荐部署以下YAML模板，使用集群水平伸缩器[cluster-proportional-autoscaler](https://github.com/kubernetes-sigs/cluster-proportional-autoscaler)动态调整副本数量。不建议使用针对QPS、CPU或MEM指标的水平伸缩器，实测效果较差。

    ```
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: dns-autoscaler
      namespace: kube-system
      labels:
        k8s-app: dns-autoscaler
    spec:
      selector:
        matchLabels:
          k8s-app: dns-autoscaler
      template:
        metadata:
          labels:
            k8s-app: dns-autoscaler
        spec:
          serviceAccountName: admin
          containers:
          - name: autoscaler
            image: registry.cn-hangzhou.aliyuncs.com/acs/cluster-proportional-autoscaler:1.8.4
            resources:
              requests:
                cpu: "200m"
                memory: "150Mi"
            command:
            - /cluster-proportional-autoscaler
            - --namespace=kube-system
            - --configmap=dns-autoscaler
            - --nodelabels=type!=virtual-kubelet
            - --target=Deployment/coredns
            - --default-params={"linear":{"coresPerReplica":64,"nodesPerReplica":8,"min":2,"max":100,"preventSinglePointFailure":true}}
            - --logtostderr=true
            - --v=9
    ```

    **说明：** 上述使用线程伸缩策略中，CoreDNS副本数的计算公式为replicas = max \(ceil \(cores × 1/coresPerReplica\), ceil \(nodes × 1/nodesPerReplica\) \)，且CoreDNS副本数受到`max`，`min`限制。

    线程伸缩策略参数如下。

    ```
    {
          "coresPerReplica": 64,
          "nodesPerReplica": 8,
          "min": 2,
          "max": 100,
          "preventSinglePointFailure": true
    }
    ```


## 使用节点级NodeLocal DNSCache

在ACK集群中部署NodeLocal DNSCache可以提升服务发现的稳定性和性能，NodeLocal DNSCache通过在集群节点上作为DaemonSet运行DNS缓存代理来提高集群DNS性能。

关于更多NodeLocal DNSCache的介绍及如何在ACK集群中部署NodeLocal DNSCache的具体步骤，请参见[在ACK集群中使用NodeLocal DNSCache](/cn.zh-CN/Kubernetes集群用户指南/网络/服务发现DNS/在ACK集群中使用NodeLocal DNSCache.md)。

**相关文档**  


[基于DNS的服务发现概述](/cn.zh-CN/Kubernetes集群用户指南/网络/服务发现DNS/基于DNS的服务发现概述.md)

[ACK集群内DNS原理和配置说明](/cn.zh-CN/Kubernetes集群用户指南/网络/服务发现DNS/ACK集群内DNS原理和配置说明.md)

[在ACK集群中使用NodeLocal DNSCache](/cn.zh-CN/Kubernetes集群用户指南/网络/服务发现DNS/在ACK集群中使用NodeLocal DNSCache.md)

