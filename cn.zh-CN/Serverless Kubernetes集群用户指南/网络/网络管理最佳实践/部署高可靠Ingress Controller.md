---
keyword: [Ingress Controller, Ingress接入层]
---

# 部署高可靠Ingress Controller

在Kubernetes集群中，Ingress是授权入站连接到达集群服务的规则集合，可以为您提供七层负载均衡能力，您可以通过Ingress配置提供外部可访问的URL、负载均衡、SSL、基于名称的虚拟主机等。作为集群流量接入层，Ingress的高可靠性显得尤为重要，本文介绍如何部署一套高性能、高可靠的Ingress接入层。

-   已创建Kubernetes集群。具体操作，请参见[创建Serverless Kubernetes集群](/cn.zh-CN/Serverless Kubernetes集群用户指南/快速入门/创建Serverless Kubernetes集群.md)。
-   SSH连接到Master节点。具体操作，请参见[通过SSH访问Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过SSH访问Kubernetes集群.md)。

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
    nginx-ingress-lb   LoadBalancer   172.XX.XX.XX   118.XX.XX.XX   80:32XXX/TCP,443:31XXX/TCP   21d
    ```


## 部署高可靠的Ingress接入层

随着集群业务规模的逐渐扩大，系统需要扩容Ingress接入层，以保证集群接入层的高性能高可用。通过调整Nginx Ingress Controller Deployment的Pod副本数量，可以快速增加Ingress接入层的规模。

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


