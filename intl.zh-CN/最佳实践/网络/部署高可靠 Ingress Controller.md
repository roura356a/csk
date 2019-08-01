# 部署高可靠 Ingress Controller {#task_1339886 .task}

作为集群流量接入层，Ingress 的高可靠性显得尤为重要，本文探讨如何部署一套高性能高可靠的 Ingress 接入层。

-   您已成功创建一个 Kubernetes 集群，参见[../../../../dita-oss-bucket/SP\_235/DNcsk1877477/ZH-CN\_TP\_16639.md\#](../../../../intl.zh-CN//创建Kubernetes 集群.md#)。
-   SSH 连接到 Master 节点，参见[SSH访问Kubernetes集群](../../../../intl.zh-CN//SSH访问Kubernetes集群.md#)。

在 Kubernetes 集群中，Ingress 是授权入站连接到达集群服务的规则集合，为您提供七层负载均衡能力，您可以通过 Ingress 配置提供外部可访问的 URL、负载均衡、SSL、基于名称的虚拟主机等。

## 高可靠部署架构 {#section_9uz_4i4_8du .section}

高可靠性首先要解决的就是单点故障问题，通常采用多副本部署的方式，在 Kubernetes 集群中部署高可靠 Ingress 接入层同样采用多节点部署架构，同时由于 Ingress 作为集群流量接入口，建议采用独占 Ingress 节点的方式，以避免业务应用与 Ingress 服务发生资源争抢。

![架构](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15817/156466016710101_zh-CN.png)

如上述部署架构图，由多个独占 Ingress 实例组成统一接入层承载集群入口流量，同时可依据后端业务流量水平扩缩容 Ingress 节点。当然如果您前期的集群规模并不大，也可以采用将 Ingress 服务与业务应用混部的方式，但建议进行资源限制和隔离。

## 查看集群默认部署的Pod副本及公网地址 {#section_fw3_4fp_ths .section}

当您成功创建一个集群后，默认情况下，集群内部已经部署了一套拥有2个Pod副本的Nginx Ingress Controller服务，其前端挂载在一个公网SLB实例上。

1.  执行以下命令查看部署Nginx Ingress Controller服务的Pod。 

    ``` {#codeblock_922_5hj_dvp}
    kubectl -n kube-system get pod | grep nginx-ingress-controller
    
    nginx-ingress-controller-8648ddc696-2bshk                    1/1     Running   0          3h
    nginx-ingress-controller-8648ddc696-jvbs9                    1/1     Running   0          3h
    ```

2.  执行以下命令查看nginx-ingress-lb服务对应的公网SLB地址。 

    ``` {#codeblock_oya_t98_ydl}
    kubectl -n kube-system get svc nginx-ingress-lb
    
    NAME               TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)                      AGE
    nginx-ingress-lb   LoadBalancer   172.xx.x.xx   118.xxx.xxx.xx   80:32457/TCP,443:31370/TCP   21d
    ```


随着集群业务规模的逐渐扩大，我们需要同时扩容Ingress接入层，以保证集群接入层的高性能高可用，可通过以下两种方法完成。

## 方法一 增加Replica数量 {#section_vo1_mo8_ivh .section}

我们可以通过简单地调整Nginx Ingress Controller Deployment的Replica数量来快速增加Ingress接入层的规模。

1.  执行以下命令，将Pod副本数量扩容到3个。 

    ``` {#codeblock_35g_8nq_26i}
    kubectl -n kube-system scale --replicas=3 deployment/nginx-ingress-controller
    deployment.extensions/nginx-ingress-controller scaled
    ```

2.  执行以下命令，查看部署Nginx Ingress Controller服务的Pod。 

    ``` {#codeblock_8ax_oqn_lj6}
    kubectl -n kube-system get pod | grep nginx-ingress-controller
    
    nginx-ingress-controller-8648ddc696-2bshk                    1/1     Running   0          3h
    nginx-ingress-controller-8648ddc696-jvbs9                    1/1     Running   0          3h
    nginx-ingress-controller-8648ddc696-xqmfn                    1/1     Running   0          33s
    ```


## 方法二 在指定节点部署Ingress服务 {#section_asx_59h_hol .section}

如果你需要Nginx Ingress Controller仅运行在指定的一些高配置节点上，可以通过给节点添加标签来完成。

1.  执行以下命令查看当前集群的节点情况。 

    ``` {#codeblock_tg6_15k_tbj}
    kubectl get node
    
    NAME                                 STATUS   ROLES    AGE   VERSION
    cn-hangzhou.i-bp11bcmsna8d4bp****   Ready    master   21d   v1.11.5
    cn-hangzhou.i-bp12h6biv9bg24l****   Ready    <none>   21d   v1.11.5
    cn-hangzhou.i-bp12h6biv9bg24l****   Ready    <none>   21d   v1.11.5
    cn-hangzhou.i-bp12h6biv9bg24l****   Ready    <none>   21d   v1.11.5
    cn-hangzhou.i-bp181pofzyyksie****   Ready    master   21d   v1.11.5
    cn-hangzhou.i-bp1cbsg6rf3580z****   Ready    master   21d   v1.11.5
    ```

2.  执行以下命令，给Ingress Node节点`cn-hangzhou.i-bp12h6biv9bg24lmdc2o`和`cn-hangzhou.i-bp12h6biv9bg24lmdc2p`添加标签`node-role.kubernetes.io/ingress="true"`。 

    **说明：** 

    -   添加标签的节点数量要大于等于集群Pod副本数，从而避免多个Pod运行在同一个节点上。
    -   不建议将Ingress服务部署到master节点上，尽量选择worker节点添加标签。

        查询结果中，ROLES的值显示为none，表示为Worker节点

    ``` {#codeblock_nzw_vj9_es5}
    kubectl label nodes cn-hangzhou.i-bp12h6biv9bg24lmdc2o node-role.kubernetes.io/ingress="true"
    node/cn-hangzhou.i-bp12h6biv9bg24lmdc2o labeled
    ```

    ``` {#codeblock_05n_7la_5q4}
    kubectl label nodes cn-hangzhou.i-bp12h6biv9bg24lmdc2p node-role.kubernetes.io/ingress="true"
    node/cn-hangzhou.i-bp12h6biv9bg24lmdc2p labeled
    ```

3.  执行以下命令，更新deployment，增加nodeSelector配置。 

    ``` {#codeblock_qrz_wpj_x61}
    kubectl -n kube-system patch deployment nginx-ingress-controller -p '{"spec": {"template": {"spec": {"nodeSelector": {"node-role.kubernetes.io/ingress": "true"}}}}}'
    deployment.extensions/nginx-ingress-controller patched
    ```


执行以下命令，查看Ingress Pod已部署在添加了标签`node-role.kubernetes.io/ingress="true"`的集群节点上。

``` {#codeblock_xk7_ctx_ewh}
kubectl -n kube-system get pod -o wide | grep nginx-ingress-controller

nginx-ingress-controller-8648ddc696-2bshk                    1/1     Running   0          3h    172.16.2.15     cn-hangzhou.i-bp12h6biv9bg24lmdc2p   <none>
nginx-ingress-controller-8648ddc696-jvbs9                    1/1     Running   0          3h    172.16.2.145    cn-hangzhou.i-bp12h6biv9bg24lmdc2o   <none>
```

