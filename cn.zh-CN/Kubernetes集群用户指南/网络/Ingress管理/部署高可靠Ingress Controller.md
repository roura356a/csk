---
keyword: [Ingress Controller, Ingress接入层]
---

# 部署高可靠Ingress Controller

在Kubernetes集群中，Ingress是授权入站连接到达集群服务的规则集合，可以为您提供七层负载均衡能力，您可以通过Ingress配置提供外部可访问的URL、负载均衡、SSL、基于名称的虚拟主机等。作为集群流量接入层，Ingress的高可靠性显得尤为重要，本文介绍如何部署一套高性能高可靠的Ingress接入层。

-   您已成功创建一个Kubernetes集群，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   SSH连接到Master节点，请参见[SSH访问Kubernets集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过SSH访问Kubernetes集群.md)。

## 高可靠部署架构

高可靠性首先要解决的就是单点故障问题，通常系统采用多副本部署的方式，在Kubernetes集群中部署高可靠Ingress接入层同样采用多节点部署架构，同时由于Ingress作为集群流量接入口，建议系统采用一个Ingress服务独占一个Ingress节点的方式，以避免业务应用与Ingress服务发生资源抢占。

![架构](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3116659951/p10101.png)

如上述部署架构图，由多个独占Ingress实例组成统一接入层来承载集群入口流量，同时可依据后端业务流量水平扩缩容Ingress节点。若您前期的集群规模并不大，也可以采用将Ingress服务与业务应用混部的方式，但注意进行资源限制和隔离。

## 查看集群默认部署的Pod副本及公网地址

当您成功创建一个集群后，默认情况下，集群内部已经部署了一套拥有2个Pod副本的Nginx Ingress Controller服务，其前端挂载在一个公网SLB实例上。

1.  执行以下命令查看部署Nginx Ingress Controller服务的Pod。

    ```
    kubectl -n kube-system get pod | grep nginx-ingress-controller
    ```

    预期输出：

    ```
    nginx-ingress-controller-8648ddc696-2bshk                    1/1     Running   0          3h
    nginx-ingress-controller-8648ddc696-jvbs9                    1/1     Running   0          3h
    ```

2.  执行以下命令查看nginx-ingress-lb服务对应的公网SLB地址。

    ```
    kubectl -n kube-system get svc nginx-ingress-lb
    ```

    预期输出：

    ```
    NAME               TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)                      AGE
    nginx-ingress-lb   LoadBalancer   172.xx.x.xx   118.xxx.xxx.xx   80:32457/TCP,443:31370/TCP   21d
    ```


## 部署高可靠的Ingress接入层

随着集群业务规模的逐渐扩大，系统需要扩容Ingress接入层，以保证集群接入层的高性能高可用，可通过以下两种方法完成。

-   **方法一：增加Pod副本数量**

    通过调整Nginx Ingress Controller Deployment的Pod副本数量，可以快速增加Ingress接入层的规模。

    1.  执行以下命令，将Pod副本数量扩容到3个。

        ```
        kubectl -n kube-system scale --replicas=3 deployment/nginx-ingress-controller
        deployment.extensions/nginx-ingress-controller scaled
        ```

    2.  执行以下命令，查看部署Nginx Ingress Controller服务的Pod。

        ```
        kubectl -n kube-system get pod | grep nginx-ingress-controller
        ```

        预期输出：

        ```
        nginx-ingress-controller-8648ddc696-2bshk                    1/1     Running   0          3h
        nginx-ingress-controller-8648ddc696-jvbs9                    1/1     Running   0          3h
        nginx-ingress-controller-8648ddc696-xqmfn                    1/1     Running   0          33s
        ```

-   **方法二：在指定的高配置节点上部署Ingress服务**

    如果您需要Nginx Ingress Controller仅运行在指定的一些高配置节点上，可以通过给节点添加标签来完成。

    1.  执行以下命令查看当前集群的节点情况。

        ```
        kubectl get node
        ```

        预期输出：

        ```
        NAME                                 STATUS   ROLES    AGE   VERSION
        cn-hangzhou.i-bp11bcmsna8d4bp****   Ready    master   21d   v1.11.5
        cn-hangzhou.i-bp12h6biv9bg24l****   Ready    <none>   21d   v1.11.5
        cn-hangzhou.i-bp12h6biv9bg24l****   Ready    <none>   21d   v1.11.5
        cn-hangzhou.i-bp12h6biv9bg24l****   Ready    <none>   21d   v1.11.5
        cn-hangzhou.i-bp181pofzyyksie****   Ready    master   21d   v1.11.5
        cn-hangzhou.i-bp1cbsg6rf3580z****   Ready    master   21d   v1.11.5
        ```

    2.  执行以下命令，给Ingress Node节点`cn-hangzhou.i-bp12h6biv9bg24lmdc2o`和`cn-hangzhou.i-bp12h6biv9bg24lmdc2p`添加标签`node-role.kubernetes.io/ingress="true"`。

        ```
        kubectl label nodes cn-hangzhou.i-bp12h6biv9bg24lmdc2o node-role.kubernetes.io/ingress="true"
        node/cn-hangzhou.i-bp12h6biv9bg24lmdc2o labeled
        ```

        ```
        kubectl label nodes cn-hangzhou.i-bp12h6biv9bg24lmdc2p node-role.kubernetes.io/ingress="true"
        node/cn-hangzhou.i-bp12h6biv9bg24lmdc2p labeled
        ```

        **说明：**

        -   添加标签的节点数量要大于等于集群Pod副本数，从而避免多个Pod运行在同一个节点上。
        -   查询结果中，若ROLES的值显示为none，则表示为Worker节点。
        -   建议您不要将Ingress服务部署到Master节点上，尽量选择Worker节点添加标签。
    3.  执行以下命令，更新Deployment，增加nodeSelector配置。

        ```
        kubectl -n kube-system patch deployment nginx-ingress-controller -p '{"spec": {"template": {"spec": {"nodeSelector": {"node-role.kubernetes.io/ingress": "true"}}}}}'
        deployment.extensions/nginx-ingress-controller patched
        ```

    4.  执行以下命令，查看Ingress Pod已部署在添加了标签`node-role.kubernetes.io/ingress="true"`的集群节点上。

        ```
        kubectl -n kube-system get pod -o wide | grep nginx-ingress-controller
        ```

        预期输出：

        ```
        nginx-ingress-controller-8648ddc696-2bshk                    1/1     Running   0          3h    172.16.2.15     cn-hangzhou.i-bp12h6biv9bg24lmdc2p   <none>
        nginx-ingress-controller-8648ddc696-jvbs9                    1/1     Running   0          3h    172.16.2.145    cn-hangzhou.i-bp12h6biv9bg24lmdc2o   <none>
        ```


