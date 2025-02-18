---
keyword: [ALB Ingress, 服务访问]
---

# 通过ALB Ingress访问服务

ALB Ingress基于阿里云应用型负载均衡ALB（Application Load Balancer） 之上提供更为强大的Ingress流量管理方式，兼容Nginx Ingress，具备处理复杂业务路由和证书自动发现的能力，支持HTTP、HTTPS和QUIC协议，完全满足在云原生应用场景下对超强弹性和大规模七层流量处理能力的需求。本文介绍如何使用ALB Ingress访问服务。

-   您已创建一个ASK集群，集群的VPC需要配置NAT网关，从而可以访问外网，下载容器镜像。具体操作，请参见[ASK使用快速入门](/intl.zh-CN/Serverless Kubernetes集群用户指南/集群/创建Serverless Kubernetes集群.md)。
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
        alb.ingress.kubernetes.io/vswitch-ids: "vsw-k1akdsmts6njkvhas****,vsw-k1amdv9ax94gr5iwa****"
        alb.ingress.kubernetes.io/healthcheck-enabled: "true"
        alb.ingress.kubernetes.io/healthcheck-path: "/"
        alb.ingress.kubernetes.io/healthcheck-protocol: "HTTP"
        alb.ingress.kubernetes.io/healthcheck-method: "HEAD"
        alb.ingress.kubernetes.io/healthcheck-httpcode: "http_2xx"
        alb.ingress.kubernetes.io/healthcheck-timeout-seconds: "5"
        alb.ingress.kubernetes.io/healthcheck-interval-seconds: "2"
        alb.ingress.kubernetes.io/healthy-threshold-count: "3"
        alb.ingress.kubernetes.io/unhealthy-threshold-count: "3"
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
    |alb.ingress.kubernetes.io/name|表示ALB实例名称。|
    |alb.ingress.kubernetes.io/address-type|表示负载均衡的地址类型。取值如下：    -   Internet（默认值）：负载均衡具有公网IP地址，DNS域名被解析到公网IP，因此可以在公网环境访问。
    -   Intranet：负载均衡只有私网IP地址，DNS域名被解析到私网IP，因此只能被负载均衡所在VPC的内网环境访问。 |
    |alb.ingress.kubernetes.io/vswitch-ids|用于设置ALB Ingress交换机ID，您需要至少指定两个不同可用区交换机ID。关于ALB Ingress支持的地域与可用区，请参见[支持的地域与可用区](/intl.zh-CN/应用型负载均衡ALB/ALB产品简介/支持的地域与可用区.md)。|
    |alb.ingress.kubernetes.io/healthcheck-enabled|表示是否开启健康检查。默认开启（true）|
    |alb.ingress.kubernetes.io/healthcheck-path|表示健康检查路径。默认/。    -   输入健康检查页面的URL，建议对静态页面进行检查。长度限制为1~80个字符，支持使用字母、数字和短划线（-）、正斜线（/）、半角句号（.）、百分号（%）、半角问号（?）、井号（\#）和and（&）以及扩展字符集\_;~!（\)\*\[\]@$^:',+。URL必须以正斜线（/）开头。
    -   HTTP健康检查默认由负载均衡系统通过后端ECS内网IP地址向该服务器应用配置的默认首页发起HTTP Head请求。如果您用来进行健康检查的页面并不是应用服务器的默认首页，需要指定具体的检查路径。 |
    |alb.ingress.kubernetes.io/healthcheck-protocol|表示健康检查协议。    -   HTTP（默认）：通过发送HEAD或GET请求模拟浏览器的访问行为来检查服务器应用是否健康。
    -   TCP：通过发送SYN握手报文来检测服务器端口是否存活。
    -   GRPC：通过发送POST或GET请求来检查服务器应用是否健康。 |
    |alb.ingress.kubernetes.io/healthcheck-method|选择一种健康检查方法。    -   HEAD（默认）：HTTP监听健康检查默认采用HEAD方法。请确保您的后端服务器支持HEAD请求。如果您的后端应用服务器不支持HEAD方法或HEAD方法被禁用，则可能会出现健康检查失败，此时可以使用GET方法来进行健康检查。
    -   POST：GRPC监听健康检查默认采用POST方法。请确保您的后端服务器支持POST请求。如果您的后端应用服务器不支持POST方法或POST方法被禁用，则可能会出现健康检查失败，此时可以使用GET方法来进行健康检查。
    -   GET：如果响应报文长度超过8 KB，会被截断，但不会影响健康检查结果的判定。 |
    |alb.ingress.kubernetes.io/healthcheck-httpcode|设置健康检查正常的状态码。    -   当健康检查协议为HTTP协议时，可以选择http\_2xx（默认）、http\_3xx、http\_4xx和http\_5xx。
    -   当健康检查协议为GRPC协议时，状态码范围为0~99。支持范围输入，最多支持20个范围值，多个范围值使用半角逗号（,）隔开。 |
    |alb.ingress.kubernetes.io/healthcheck-timeout-seconds|表示接收健康检查的响应需要等待的时间。如果后端ECS在指定的时间内没有正确响应，则判定为健康检查失败。时间范围为1~300秒，默认值为5秒。|
    |alb.ingress.kubernetes.io/healthcheck-interval-seconds|健康检查的时间间隔。取值范围1~50秒，默认为2秒。|
    |alb.ingress.kubernetes.io/healthy-threshold-count|表示健康检查连续成功所设置的次数后会将后端服务器的健康检查状态由失败判定为成功。取值范围2~10，默认为3次。|
    |alb.ingress.kubernetes.io/unhealthy-threshold-count|表示健康检查连续失败所设置的次数后会将后端服务器的健康检查状态由成功判定为失败。取值范围 2~10，默认为3次。|

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

-   利用获取的ALB实例IP地址，通过以下两种方式访问`tea`服务：
    -   通过浏览器访问`tea`服务。

        ![SLB2](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/7381296261/p297351.png)

    -   通过命令行方式访问`tea`服务。

        ```
        curl http://alb-m551oo2zn63yov****.cn-hangzhou.alb.aliyuncs.com/tea
        ```


