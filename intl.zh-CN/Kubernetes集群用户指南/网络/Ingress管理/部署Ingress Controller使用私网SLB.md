---
keyword: [私网SLB访问Kubernetes服务, Ingress Controller]
---

# 部署Ingress Controller使用私网SLB

容器服务集群可以允许公网访问，也可以被同一个VPC下的其他服务直接访问。本文介绍如何配置阿里云容器服务Kubernetes Ingress Controller使用私网负载均衡SLB（Server Load Balancer）。

-   您已成功创建一个容器服务Kubernetes集群，请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   SSH连接到Master节点，请参见[通过SSH访问Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过SSH访问Kubernetes集群.md)。

通过阿里云容器服务管理控制台创建的Kubernetes集群在初始化时会自动部署一套Nginx Ingress Controller，默认其挂载在公网SLB实例上。

![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8063659951/p81310.png)

## 配置私网SLB

您可以调整Nginx Ingress Controller服务的配置，使容器集群内的服务仅仅只对同一个VPC内其他服务调用访问。

![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8063659951/p81311.png)

1.  手动申请私网SLB实例，请参见[创建负载均衡实例](/intl.zh-CN/传统型负载均衡CLB/CLB用户指南/实例/创建负载均衡实例.md)。

    **说明：** 在对应的VPC网络下申请一个期望规格的SLB实例。

2.  配置Nginx Ingress Controller服务。

    当您成功创建一个私网SLB实例后，您可以通过以下示例注解来配置Nginx Ingress Controller使用该SLB实例，请参见[通过Annotation配置负载均衡](/intl.zh-CN/Kubernetes集群用户指南/网络/Service管理/通过Annotation配置负载均衡.md)。

    ```
    # nginx ingress slb service
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx-ingress-lb
      namespace: kube-system
      labels:
        app: nginx-ingress-lb
      annotations:
        # 指明SLB实例地址类型为私网类型。
        service.beta.kubernetes.io/alicloud-loadbalancer-address-type: intranet
        # 修改为您的私网SLB实例ID。
        service.beta.kubernetes.io/alicloud-loadbalancer-id: <YOUR_INTRANET_SLB_ID>
        # 是否自动创建SLB端口监听（会覆写已有端口监听），也可手动创建端口监听。
        #service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners: 'true'
    spec:
      type: LoadBalancer
      # route traffic to other nodes
      externalTrafficPolicy: "Cluster"
      ports:
      - port: 80
        name: http
        targetPort: 80
      - port: 443
        name: https
        targetPort: 443
      selector:
        # select app=ingress-nginx pods
        app: ingress-nginx
    ```

    通过以上注解修改（apply）容器集群kube-system/nginx-ingress-lb服务即可配置使用指定的私网SLB实例。


## 同时使用私网SLB和公网SLB

对于一些特殊场景，您期望容器集群内的服务既能允许公网访问，同时又希望能被同一个VPC下的其他服务直接访问（不经过公网）。您只需额外部署一个kube-system/nginx-ingress-lb-intranet服务。

![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8063659951/p81334.png)

**说明：** 默认集群初始化时已经创建了一个kube-system/nginx-ingress-lb服务，其生成的是公网SLB实例。

1.  手动申请私网SLB实例，请参见[创建负载均衡实例](/intl.zh-CN/传统型负载均衡CLB/CLB用户指南/实例/创建负载均衡实例.md)。

    **说明：** 在对应的VPC网络下申请一个期望规格的SLB实例。

2.  新建Intranet Nginx Ingress Controller服务。

    当您成功申请一个私网SLB实例后，您可以通过以下YAML新建一个kube-system/nginx-ingress-lb-intranet服务。

    ```
    # intranet nginx ingress slb service
    apiVersion: v1
    kind: Service
    metadata:
      # 这里服务取名为nginx-ingress-lb-intranet。
      name: nginx-ingress-lb-intranet
      namespace: kube-system
      labels:
        app: nginx-ingress-lb-intranet
      annotations:
        # 指明SLB实例地址类型为私网类型。
        service.beta.kubernetes.io/alicloud-loadbalancer-address-type: intranet
        # 修改为您的私网SLB实例ID。
        service.beta.kubernetes.io/alicloud-loadbalancer-id: <YOUR_INTRANET_SLB_ID>
        # 是否自动创建SLB端口监听（会覆写已有端口监听），也可手动创建端口监听。
        #service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners: 'true'
    spec:
      type: LoadBalancer
      # route traffic to other nodes
      externalTrafficPolicy: "Cluster"
      ports:
      - port: 80
        name: http
        targetPort: 80
      - port: 443
        name: https
        targetPort: 443
      selector:
        # select app=ingress-nginx pods
        app: ingress-nginx
    ```


成功创建kube-system/nginx-ingress-lb-intranet服务后，您可以通过`kubectl -n kube-system get svc | grep nginx-ingress-lb`命令看到以下两个Nginx Ingress Controller服务：一个配置的是公网SLB实例，一个是VPC私网SLB实例。

```
 kubectl -n kube-system get svc | grep nginx-ingress-lb
```

```
nginx-ingress-lb            LoadBalancer   172.1*.*.**    47.96.2**.**   80:31456/TCP,443:30016/TCP   5h
nginx-ingress-lb-intranet   LoadBalancer   172.19.*.***   192.16*.*.**   80:32394/TCP,443:31000/TCP   7m
```

此时当您通过Ingress对外暴露服务时，即可以通过公网SLB来访问该服务，同一个VPC下的其他服务也可以直接通过私网SLB来访问该服务。

