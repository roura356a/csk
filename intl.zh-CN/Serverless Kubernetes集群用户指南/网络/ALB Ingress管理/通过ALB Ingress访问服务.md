---
keyword: [ALB Ingress, 服务访问]
---

# 通过ALB Ingress访问服务

ALB Ingress基于阿里云应用型负载均衡ALB（Application Load Balancer） 之上提供更为强大的Ingress流量管理方式，兼容Nginx Ingress，具备处理复杂业务路由和证书自动发现的能力，支持HTTP、HTTPS和QUIC协议，完全满足在云原生应用场景下对超强弹性和大规模七层流量处理能力的需求。本文介绍如何使用ALB Ingress访问服务。

-   您已创建一个ASK集群，集群的VPC需要配置NAT网关，从而可以访问外网，下载容器镜像。具体操作，请参见[创建Serverless Kubernetes集群](/intl.zh-CN/Serverless Kubernetes集群用户指南/快速入门/创建Serverless Kubernetes集群.md)。
-   您已通过kubectl连接到集群。具体操作，请参见[通过kubectl连接Kubernetes集群](/intl.zh-CN/Serverless Kubernetes集群用户指南/集群/管理和访问集群/通过kubectl连接Kubernetes集群.md)。

Ingress是允许访问集群内Service的规则集合，您可以通过配置转发规则，实现不同URL访问集群内不同的Service。但传统的Nginx Ingress或者四层SLB Ingress，已无法满足云原生应用服务对复杂业务路由、多种应用层协议（例如：QUIC等）、大规模七层流量能力的需求。

## 步骤一：部署服务

1.  创建并拷贝以下内容到cafe-service.yaml文件中，用于部署两个名称分别为`coffee`和`tea`的Deployment，以及两个名称分别为`coffee`和`tea`的Service。

    ```
    apiVersion: apps/v1 
    kind: Deployment
    metadata:
      name: coffee
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: coffee
      template:
        metadata:
          labels:
            app: coffee
        spec:
          containers:
          - name: coffee
            image: registry.cn-hangzhou.aliyuncs.com/acs-sample/nginxdemos:latest
            ports:
            - containerPort: 80
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: coffee-svc
    spec:
      ports:
      - port: 80
        targetPort: 80
        protocol: TCP
      selector:
        app: coffee
      clusterIP: None
    ---
    apiVersion: apps/v1 
    kind: Deployment
    metadata:
      name: tea
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: tea 
      template:
        metadata:
          labels:
            app: tea 
        spec:
          containers:
          - name: tea 
            image: registry.cn-hangzhou.aliyuncs.com/acs-sample/nginxdemos:latest
            ports:
            - containerPort: 80
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: tea-svc
      labels:
    spec:
      ports:
      - port: 80
        targetPort: 80
        protocol: TCP
      selector:
        app: tea
      clusterIP: None
    ```

2.  执行以下命令，部署两个Deployment和两个Service。

    ```
    kubectl apply -f cafe-service.yaml
    ```

    预期输出：

    ```
    deployment "coffee" created
    service "coffee-svc" created
    deployment "tea" created
    service "tea-svc" created
    ```

3.  执行以下命令，查看服务状态。

    ```
    kubectl get svc,deploy
    ```

    预期输出：

    ```
    NAME             TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
    svc/coffee-svc   ClusterIP   <none>       <none>        80/TCP    1m
    svc/tea-svc      ClusterIP   <none>       <none>        80/TCP    1m
    NAME            DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
    deploy/coffee   2         2         2            2           1m
    deploy/tea      1         1         1            1           1m
    ```


## 步骤二：配置Ingress

1.  创建并拷贝以下内容到cafe-ingress.yaml文件中。

    ```
    apiVersion: networking.k8s.io/v1beta1
    kind: Ingress
    metadata:
      name: cafe-ingress
      annotations:
        kubernetes.io/ingress.class: alb
        alb.ingress.kubernetes.io/name: ingres_test_base
        alb.ingress.kubernetes.io/address-type: internet
        alb.ingress.kubernetes.io/vswitch-ids: "vsw-k1akdsmts6njkvhasriop,vsw-k1amdv9ax94gr5iwamuwu"
    spec:
      rules:
      - http:
          paths:
          # 配置Context Path
          - path: /tea
            backend:
              serviceName: tea-svc
              servicePort: 80
          # 配置Context Path
          - path: /coffee
            backend:
              serviceName: coffee-svc
              servicePort: 80
    ```

    相关参数解释如下表所示：

    |参数|说明|
    |--|--|
    |alb.ingress.kubernetes.io/name|可选参数，表示ALB实例名称。|
    |alb.ingress.kubernetes.io/address-type|可选参数，表示负载均衡的地址类型。取值如下：    -   Internet（默认值）：负载均衡具有公网IP地址，DNS域名被解析到公网IP，因此可以在公网环境访问。
    -   Intranet：负载均衡只有私网IP地址，DNS域名被解析到私网IP，因此只能被负载均衡所在VPC的内网环境访问。 |
    |alb.ingress.kubernetes.io/vswitch-ids|用于设置ALB Ingress交换机ID，您需要至少指定两个不同可用区交换机ID。关于ALB Ingress支持的地域与可用区，请参见[支持的地域与可用区](/intl.zh-CN/应用型负载均衡ALB/ALB产品简介/支持的地域与可用区.md)。|

2.  执行以下命令，配置`coffee`和`tea`服务对外暴露的域名和`path`路径。

    ```
    kubectl apply -f cafe-ingress.yaml
    ```

    预期输出：

    ```
    ingress "cafe-ingress" created
    ```

3.  执行以下命令获取ALB实例IP地址。

    ```
    kubectl get ingress
    ```

    预期输出：

    ```
    NAME           CLASS    HOSTS   ADDRESS                                               PORTS   AGE
    cafe-ingress   <none>   *       alb-m551oo2zn63yov****.cn-hangzhou.alb.aliyuncs.com   80      50s
    ```


## 步骤三：访问服务

-   利用获取的ALB实例IP地址，通过以下两种方式访问`coffee`服务：
    -   通过浏览器访问`coffee`服务。

        ![1](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/7381296261/p297352.png)

    -   通过命令行方式访问`coffee`服务。

        ```
        curl http://alb-m551oo2zn63yov****.cn-hangzhou.alb.aliyuncs.com/coffee
        ```

-   利用获取的ALB实例IP地址，通过以下两种方式访问`tee`服务：
    -   通过浏览器访问`tee`服务。

        ![SLB2](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/7381296261/p297351.png)

    -   通过命令行方式访问`tee`服务。

        ```
        curl http://alb-m551oo2zn63yov****.cn-hangzhou.alb.aliyuncs.com/tea
        ```


