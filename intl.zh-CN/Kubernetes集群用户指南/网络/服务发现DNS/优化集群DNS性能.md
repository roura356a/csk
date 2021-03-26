---
keyword: [CoreDNS, 容器服务Kubernetes, DNS]
---

# 优化集群DNS性能

CoreDNS是Kubernetes集群中负责DNS解析的组件，能够支持解析集群内部自定义服务域名和集群外部域名。CoreDNS具备丰富的插件集，在集群层面支持自建DNS、自定义hosts、CNAME、rewrite等需求。ACK集群中默认部署形态在DNS QPS较高场景下，可能会出现解析压力，导致部分查询失败。本文介绍如何优化集群DNS性能。

-   [创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)
-   [通过kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)

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
            image: registry.cn-hangzhou.aliyuncs.com/ringtail/cluster-proportional-autoscaler-amd64:v1.3.0
            resources:
                requests:
                    cpu: "200m"
                    memory: "150Mi"
            command:
              - /cluster-proportional-autoscaler
              - --namespace=kube-system
              - --configmap=dns-autoscaler
              - --target=Deployment/coredns
              - --default-params={"linear":{"coresPerReplica":64,"nodesPerReplica":8,"min":2,"max":100,"preventSinglePointFailure":true}}
              - --logtostderr=true
              - --v=2
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


## 使用Autopath插件

在您使用Autopath插件前，需了解Pod的四种DNS策略：

-   **Default** : Pod直接继承Node节点的域名解析配置。在ACK集群即直接使用ECS的/etc/resolv.conf文件，文件内配置的是阿里云DNS服务。
-   **ClusterFirst**：通过CoreDNS来做集群内外部域名的服务发现，Pod内/etc/resolv.conf配置的DNS服务地址是集群DNS服务的地址kube-dns。

    目前，在ClusterFirst模式下，2次（1次IPv4，1次IPv6）集群外部域名查询产生8次（4次IPv4，4次IPv6）查询请求。例如，解析www.aliyun.com域名，会先分别携带三个集群主域名后缀，产生六次无效查询请求，这样会导致集群DNS QPS放大三倍，影响性能。

    ```
    2020-06-04T20:46:35.643+08:00 [INFO] 172.20.0.100:54153 - 63230 "AAAA IN www.aliyun.com.kube-system.svc.cluster.local. udp 62 false 512" NXDOMAIN qr,aa,rd 155 0.000282418s
    2020-06-04T20:46:35.644+08:00 [INFO] 172.20.0.100:54153 - 62219 "A IN www.aliyun.com.kube-system.svc.cluster.local. udp 62 false 512" NXDOMAIN qr,aa,rd 155 0.000387552s
    2020-06-04T20:46:35.644+08:00 [INFO] 172.20.0.100:33409 - 11389 "AAAA IN www.aliyun.com.svc.cluster.local. udp 50 false 512" NXDOMAIN qr,aa,rd 143 0.000264026s
    2020-06-04T20:46:35.644+08:00 [INFO] 172.20.0.100:33409 - 10963 "A IN www.aliyun.com.svc.cluster.local. udp 50 false 512" NXDOMAIN qr,aa,rd 143 0.000334383s
    2020-06-04T20:46:35.645+08:00 [INFO] 172.20.0.100:57153 - 2741 "AAAA IN www.aliyun.com.cluster.local. udp 46 false 512" NXDOMAIN qr,aa,rd 139 0.000200375s
    2020-06-04T20:46:35.645+08:00 [INFO] 172.20.0.100:57153 - 2435 "A IN www.aliyun.com.cluster.local. udp 46 false 512" NXDOMAIN qr,aa,rd 139 0.000329507s
    2020-06-04T20:46:35.646+08:00 [INFO] 172.20.0.100:48284 - 31225 "A IN www.aliyun.com. udp 32 false 512" NOERROR qr,aa,rd,ra 476 0.00070823s
    2020-06-04T20:46:35.646+08:00 [INFO] 172.20.0.100:48284 - 31851 "AAAA IN www.aliyun.com. udp 32 false 512" NOERROR qr,aa,rd,ra 498 0.000925332s
    ```

-   **ClusterFirstWithHostNetwork**：强制在hostNetWork网络模式下使用ClusterFirst策略（默认使用Default策略）。
-   **None**：忽略集群DNS策略，需要用户提供dnsConfig字段来指定DNS配置信息。

Autopath会在第一次域名查询失败时切割域名后缀，尝试找到正确的域名，做到2次（1次IPv4，1次IPv6）域名查询获取到正确的解析结果。

**说明：**

-   创建1.16版本的集群时已开启Autopath功能。低版本集群未开启该功能。
-   Autopath插件会降低CoreDNS Pod的CPU使用率，但是会增加内存（实测1000节点，10000规模Pod占用不超过100 Mi）。

**使用Autopath**

参照以下模板，按照红框内容改动文件kube-system/configmap coredns。

![autopath](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6795659951/p129419.png)

## 使用节点级Node Local DNS

在ACK集群中部署Node Local DNS可以提升服务发现的稳定性和性能。Node Local DNS以DaemonSet形式部署，会在ACK集群的每个节点上运行一个专门处理DNS查询请求的Pod，该Pod会将集群内部域名查询请求发往CoreDNS，将集群外部请求直接发往外部域名解析服务器。同时能够缓存所有请求。可以被看作是节点级别的高效DNS缓存，能够大幅提高集群整体DNS查询的QPS。

关于更多Node Local DNS的介绍及如何在ACK集群中部署Node Local DNS的具体步骤，请参见[在ACK集群中部署Node Local DNS](/intl.zh-CN/Kubernetes集群用户指南/网络/服务发现DNS/在ACK集群中部署Node Local DNS.md)。

**相关文档**  


[基于DNS的服务发现概述](/intl.zh-CN/Kubernetes集群用户指南/网络/服务发现DNS/基于DNS的服务发现概述.md)

[ACK集群内DNS原理和配置说明](/intl.zh-CN/Kubernetes集群用户指南/网络/服务发现DNS/ACK集群内DNS原理和配置说明.md)

[在ACK集群中部署Node Local DNS](/intl.zh-CN/Kubernetes集群用户指南/网络/服务发现DNS/在ACK集群中部署Node Local DNS.md)

