---
keyword: [Ingress Controller, 流量复制, 仿真测试]
---

# 通过Ingress Controller来实现应用的流量复制

ACK支持在不同集群内进行流量复制，用于系统的仿真测试和问题定位。本文介绍如何通过Ingress Controller来实现应用的流量复制。

已创建两个ACK集群，具体操作，请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。本例中ACK集群一个名为K8s Product Cluster（生产环境集群），另一个名为K8s Stage Cluster（测试环境集群）。

## 应用场景

流量复制（Traffic Mirror）功能应用于以下两个场景：

-   在系统进行重大重构或者发布新功能时，往往需要对系统进行压测以事先评估新系统的承载能力。传统情况下，在线下环境模拟线上各种测试数据来对新系统进行测试，但是这种方式往往并不能有效地模拟出线上真实的访问量，尤其是模拟正常流量中混杂着各色的异常流量。针对这种情况，可以通过将线上的应用流量复制到指定的线下环境来对新系统进行仿真测试。
-   线上系统遇到性能瓶颈，但是又不能快速地定位出问题时，这时可以采用流量复制的方式来将应用的真实流量引导到线下环境来进行问题定位。

    ![应用场景](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2689850161/p210897.png)


## 步骤一：部署基础应用

1.  在K8s Product Cluster集群部署应用，并测试访问。

    1.  在K8s Product Cluster集群中部署一个应用，并通过Ingress来对外暴露服务访问。

        ```
        apiVersion: extensions/v1beta1
        kind: Deployment
        metadata:
          name: nginx-deployment
        spec:
          replicas: 1
          selector:
            matchLabels:
              app: nginx
          template:
            metadata:
              labels:
                app: nginx
            spec:
              containers:
              # 当前配置为老版本镜像。
              - image: registry.cn-hangzhou.aliyuncs.com/xianlu/old-nginx
                imagePullPolicy: Always
                name: nginx
                ports:
                - containerPort: 80
                  protocol: TCP
              restartPolicy: Always
        ---
        apiVersion: v1
        kind: Service
        metadata:
          name: nginx-service
        spec:
          ports:
          - port: 80
            protocol: TCP
            targetPort: 80
          selector:
            app: nginx
          type: NodePort
        ---
        apiVersion: extensions/v1beta1
        kind: Ingress
        metadata:
          name: nginx-ingress
        spec:
          rules:
          # 这里配置使用了集群默认域名（您也可以使用自定义域名并作DNS解析）。
          - host: nginx.c37bf6b77bded43669ba2fb67448b****.cn-hangzhou.alicontainer.com
            http:
              paths:
              - path: /
                backend:
                  serviceName: nginx-service
                  servicePort: 80
        ```

    2.  执行以下命令，查看该应用的Ingress配置。

        ```
        kubectl get ing nginx-ingress
        ```

        预期输出：

        ```
        NAME            HOSTS                                                                  ADDRESS         PORTS     AGE
        nginx-ingress   nginx.c37bf6b77bded43669ba2fb67448b****.cn-hangzhou.alicontainer.com   47.110.1**.**   80        8m
        ```

    3.  执行以下命令，测试访问应用域名。

        ```
        curl http://nginx.c37bf6b77bded43669ba2fb67448b****.cn-hangzhou.alicontainer.com
        ```

2.  在K8s Stage Cluster部署一个相同应用，并测试访问。

    1.  在K8s Stage Cluster中部署一个相同应用，同样通过Ingress来对外暴露服务访问。

        ```
        apiVersion: extensions/v1beta1
        kind: Deployment
        metadata:
          name: nginx-deployment
        spec:
          replicas: 1
          selector:
            matchLabels:
              app: nginx
          template:
            metadata:
              labels:
                app: nginx
            spec:
              containers:
              # 当前配置为新版本镜像。
              - image: registry.cn-hangzhou.aliyuncs.com/xianlu/new-nginx
                imagePullPolicy: Always
                name: nginx
                ports:
                - containerPort: 80
                  protocol: TCP
              restartPolicy: Always
        ---
        apiVersion: v1
        kind: Service
        metadata:
          name: nginx-service
        spec:
          ports:
          - port: 80
            protocol: TCP
            targetPort: 80
          selector:
            app: nginx
          type: NodePort
        ---
        apiVersion: extensions/v1beta1
        kind: Ingress
        metadata:
          name: nginx-ingress
        spec:
          rules:
          # 这里配置使用了集群默认域名（您也可以使用自定义域名并作DNS解析）。
          - host: nginx.c41eb6ca34a3e49f7aea63b8bc9e8****.cn-beijing.alicontainer.com
            http:
              paths:
              - path: /
                backend:
                  serviceName: nginx-service
                  servicePort: 80
        ```

    2.  执行以下命令，查看该应用的Ingress配置。

        ```
        kubectl get ing nginx-ingress
        ```

        预期输出：

        ```
        NAME            HOSTS                                                                 ADDRESS        PORTS     AGE
        nginx-ingress   nginx.c41eb6ca34a3e49f7aea63b8bc9e8****.cn-beijing.alicontainer.com   39.106.***.*   80        1m
        ```

    3.  执行以下命令，测试访问应用域名。

        ```
        curl http://nginx.c41eb6ca34a3e49f7aea63b8bc9e8****.cn-beijing.alicontainer.com
        ```


## 步骤二：配置流量复制

将K8s Product Cluster中该应用100%的访问流量复制到K8s Stage Cluster中对应的应用服务上，即将所有访问域名`nginx.c37bf6b77bded43669ba2fb67448b****.cn-hangzhou.alicontainer.com`的请求复制一份转发到`nginx.c41eb6ca34a3e49f7aea63b8bc9e8****.cn-beijing.alicontainer.com`。

**说明：** K8s Stage Cluster Ingress仅仅只作为复制流量的接收方，不需要做任何配置修改，只需要在K8s Product Cluster Ingress上进行配置即可。

![流量复制](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6245330161/p210900.png)

1.  执行以下命令，编辑nginx-configuration，配置复制流量百分比和接收复制流量的应用域名。

    ```
    kubectl -n kube-system edit cm nginx-configuration
    ```

    在nginx-configuration中增加以下内容。

    ```
    http-snippet: |
       split_clients "$date_gmt" $mirror_servers {
          100%    nginx.c41eb6ca34a3e49f7aea63b8bc9e8****.cn-beijing.alicontainer.com;
       }
    
    # 配置说明：
    # 1. 流量百分比取值范围：(0, 100]，百分比总和必须不大于100%。
    # 2. 支持同时配置多个不同的复制流量接收目标应用。
    ```

2.  执行以下命令，编辑nginx-ingress。

    通过configuration-snippet和server-snippet修改源Ingress，增加应用的流量复制配置。

    ```
    kubectl  edit ingress nginx-ingress
    ```

    根据实际需求修改nginx-ingress的`configuration-snippet`和`server-snippet`，以下为本例修改后的YAML。

    ```
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: nginx-ingress
      annotations:
        nginx.ingress.kubernetes.io/configuration-snippet: |
            mirror /mirror; # 配置N次该项则可放大N倍流量
        nginx.ingress.kubernetes.io/server-snippet: |
            location = /mirror {
                internal;
                # 不打印mirror请求日志
                #access_log off; 
                # 设置proxy_upstream_name，格式必须为[Namespace]-[BackendServiceName]-[BackendServicePort]
                set $proxy_upstream_name    "default-nginx-service-80";
                # 自定义字符串，会作为请求头X-Shadow-Service值传给mirror server
                set $shadow_service_name    "nginx-product-service"; 
                proxy_set_header X-Shadow-Service  $shadow_service_name;
                proxy_set_header Host $mirror_servers;
                proxy_pass http://$mirror_servers$request_uri;
            }
    spec:
      rules:
      - host: nginx.c37bf6b77bded43669ba2fb67448b****.cn-hangzhou.alicontainer.com
        http:
          paths:
          - path: /
            backend:
              serviceName: nginx-service
              servicePort: 80
    ```


## 结果验证

访问K8S Product Cluster中的应用域名`nginx.c37bf6b77bded43669ba2fb67448b****.cn-hangzhou.alicontainer.com`。执行以下命令，可以看到每次请求K8S Product Cluster中的Ingress应用域名时，其都将复制一份同样的请求转发到K8S Stage Cluster中对应的应用服务中。

```
kubectl -n kube-system logs --tail-o -f nginx-ingress-controller-674c96ffbc-9mc8n
```

![流量复制结果验证](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4411330161/p211191.png)

